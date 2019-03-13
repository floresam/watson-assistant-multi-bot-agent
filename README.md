# Compose domain specific bots using an agent bot
A domain specific bot addresses queries related to a specific domain or topic. Some examples are - Travel bot(Travel related conversations), Weather bot (Weather related conversations). 

If a user wants to have a cross-domain conversation, then the user will have to switch between the different bots. 

There are scenarios where an user wants to have a conversation involving multiple domains.
For example - When I want to travel to a place, I want to query for the weather and then book a cab or flight. I might have to end up switching between two bots, weather bot and travel bot. What if I could just have one interface bot which will redirect my messages to a specific bot and get answers to me? 

Well, this code pattern showcases an implementation of this approach.

The solution here is to have an agent bot (or an interface bot) and a few other bots which can handle conversations for a specific domain - let's call these specific bots. The agent bot knows about the specific bots and also about which domain each of them can handle. When user initiates conversation with agent bot, the agent bot will understand the intent of user query and it will redirect the user query to a specific bot. Subsequent requests from user are redirected to specific bot. When the conversation with the specific bot is over or when the specific bot is not able to handle the request, the control is given back to agent bot which will then redirect the messages to appropriate bot.

This approach provides seamless experience for users. It can be used by organisations which provide a host of services to its customers like financial services, tours and travel agencies, news agencies etc..

Advantages with this approach are:
- Plug and play the bots
- Modular approach facilitates Bots composition
- Come up with new services by composing two or more Bots
- Easy to maintain, make changes, add/remove functionalities
- Easy to troubleshoot issues
- Transparent to user

In this code pattern we will use Watson assistant Bot for building Bots and Node.js application as orchestration layer.

When the reader has completed this Code Pattern, they will understand how to:

* How to configure a bot to make it Agent Bot
* How to configure a specific bot to return control to Agent Bot
* How to build an orchestration layer to stitch Agent Bot and specific Bots

## Flow

![Architecture](images/architecture.png)

1. User accesses web application and types in a message. Nodejs application, an orchestration layer, sends user message to agent bot
2. Agent bot determines the intent of the message and responds with details about the specific bot to which the message needs to be redirected.
3. Node.js application sends message to the specific bot (Weather Bot, in this case). Specific bot responds. Conversation continues between user and specific bot.
4. When the conversation with specific bot is over, user message is then sent to agent bot to determine the intent.
5. Node.js application sends message to the specific bot (Travel Bot, in this case). Specific bot responds. Conversation continues between user and specific bot.


# Watch the Video
[![](https://img.youtube.com/vi/godEYin0IYA/0.jpg)](https://youtu.be/godEYin0IYA)

# Steps
1. Clone git repo.
2. Create bots.
3. Configure application with bots details.
4. Deploy application to IBM Cloud.
5. Run application.

## 1. Clone git repo

- Navigate to the folder you'd like to store this demo repository (e.g. `Documents`)
- On command prompt run the below command to clone the git repo.
```
git clone git@github.com:IBM/watson-assistant-multi-bot-agent.git
```
or
```
git clone https://github.com/IBM/watson-assistant-multi-bot-agent.git
```
run `cd watson-assistant-multi-bot-agent` to change directory to project parent folder


## 2. Create bots

### 2.1 Create Watson Assistant service instance
- Click this [link](https://console.bluemix.net/catalog/services/watson-assistant-formerly-conversation) to create Watson assistant service.
- You can choose to enter any name you like under `Service Name`, such as `Watson-Assistant-Service`. 
- Feel free to use the default region/location, `Dallas`, and use the default resource group. 
- Under `Pricing Plans`, select `Lite` plan.
- Click `Create`.
- Watson Assistant service instance should get created.

### 2.2 Import bots
- If you are not redirected to the Watson Assistant service you just made, navigate to service by following these steps:
    - Go to the IBM Cloud dashboard (the home page)
    - click on the Watson Assistant service instance created in above steps, in `Services`.
- On the Assistant Dashboard, click `Launch Tool`.

![LaunchTool](images/launch_tool.png)

- Click `Skills` tab.

![SkillsTab](images/SkillsTab.png)

- Click `Create New` button.
- Click on `Import Skill` tab.

![ImportSkill](images/ImportSkill.png)

- Click on `Choose JSON file`.
- In your file explorer, navigate to the cloned repository parent folder, `watson-assistant-multi-bot-agent`
- Browse to folder `WA`.
- Select `agent-bot.json` and click `Open`.
- Select the option `Everything (Intents, Entities, and Dialog)`.

![ImportAWorkspace](images/import_a_workspace.png)

- Click `Import` button.
- Navigate back to the `Skills` page to repeat above steps in section [Import bots](#22-import-bots) to import `travel_bot.json` and `weather_bot.json`.

## 3. Configure application with bots details

### 3.1 Gather required details

- If not already in the `Skills` page of the your Watson Assistance service instance,
    - Go back to the IBM Cloud dashboard and click on the Watson Assistant service instance again
    - On the Assistant Dashboard, click `Launch Tool`.
    - Click `Skills` tab.

![SkillsTab](images/SkillsTab.png)

- On `agentBot` click `actions`, the three vertical dots on the top right corner.

![BotActions](images/bot_actions.png)

- Click `View API Details`.
- Copy and save `Workspace ID` for in a text file for later use, along with a note about which bot it came from.
- Repeat above steps for all the other bots as well. You should end up 3 workspace IDs, one for each bot (Agent, Weather, and Travel)

- Go back to your Watson Assistance Service instance (back on `cloud.ibm.com`)
- Copy `API Key` and `Url` using the copy buttons as shown in below image or using the `Show Credentials` and copying the field contents. Save them in a text file for later use.
![CopyAPIKeyAndUrl](images/CopyAPIKeyAndUrl.png)


### 3.2 Update manifest.yml file with the details gathered

- Back in the `watson-assistant-multi-bot-agent` project parent folder, open `manifest.yml` file in a text editor
- Update `ASSISTANT_IAM_API_KEY` with Watson Assistant service instance's API Key as noted in section [Gather required details](#31-gather-required-details)
- Update `ASSISTANT_IAM_URL` with Watson Assistant service instance's Url as noted in section [Gather required details](#31-gather-required-details)
- Update `WORKSPACE_ID_AGENT`, `WORKSPACE_ID_TRAVEL`, `WORKSPACE_ID_WEATHER` with Workspace IDs of respective bots as noted in section [Gather required details](#31-gather-required-details)

Updated `manifest.yml` file should look like the image below. Make sure to have a space after the colon. Save this file when completed.

![Manifest](images/manifest.png)


## 4. Deploy application to IBM Cloud
- On command prompt, navigate to project parent folder `watson-assistant-multi-bot-agent`
- On command prompt, login to IBM Cloud using `ibmcloud login` or `ibmcloud login --sso` (for federated login).
- Ensure that you are in the right organisation, space and region using the below command.
```
ibmcloud target --cf
```
- Run the below command to deploy the application to IBM Cloud.
```
ibmcloud cf push
```
- Check the logs of the application using the command `ibmcloud cf logs <app_name> --recent`.
- Ensure that the application is deployed to IBM Cloud successfully. If you see any errors in logs, fix them and redeploy the application.
    - If the above steps don't work, try the following:
    - Sign into us-south: `ibmcloud login -sso -a api.ng.bluemix.net`
    - Set target: `ibmcloud target --cf`
    - Try push :`ibmcloud cf push`


## 5. Run the application
- [Login](https://console.bluemix.net/) to IBM Cloud and go to dashboard. The application you deployed should show up and it should be in running state.
- Click on the application and click on `Visit App URL`.

![VisitAppURL](images/visit_app_url.png)

- The application home page opens.

![AppHomePage](images/app_home_page.png)

- In command prompt, monitor logs using the command
```
ibmcloud cf logs watson-assistant-multi-bot-agent
```
* _Note: if the application fails to recognize your query, wait several minutes. Watson may still be training in the background._

- On the application home page type a weather related query `What does the weather look like tomorrow?`.
![Query1](images/query_1.png)
- Check the log files and notice that the message first goes to Agent Bot and then it is redirected to the Weather Bot. 
![AppLogs1](images/app_logs_1.png)
- In the interface, you are asked to enter the location. Enter a location, e.g. `Bengaluru`.
- Check the logs. Because the conversation with the Weather Bot has not ended, subsequent messages are sent to the Weather Bot itself, without the intervention of the Agent Bot.
![AppLogs2](images/app_logs_2.png)
- The Weather Bot responds with an answer to user query and hence conversation with Weather Bot is treated as ended.
- Next user enters a travel related query `Book a cab`.
- In the logs, notice that the message is sent to Agent Bot and then it is redirected to Travel Bot.
![AppLogs3](images/app_logs_3.png)
- In the interface you are asked to enter a date for cab booking. Enter a date or you can just say `Today`.
- Check the logs. Because the conversation with the Travel Bot has not ended, subsequent messages are sent to the Weather Bot itself, without the intervention of the Agent Bot.
![AppLogs4](images/app_logs_4.png)
- In the interface you are asked to enter time for the cab to arrive. Say `12 PM`.
- Check the logs. Travel Bot responds with an answer. The conversation with Travel Bot has ended.
- The above conversation flow can continue and the Agent Bot will redirect the messages to specific bots based on the intent of user query
- Some of the basic messages as greetings and bye can be handled by Agent Bot itself.
- In the interface type `Thank you. Bye`

![ChatInterfaceFull](images/chat_interface_full.png)

- Overall Flow of messages between bots is as shown in the below diagram

![AppLogs](images/app_logs.png)

Legend for above image
1. Message sent to Agent Bot.
2. Message redirected to Weather Bot.
3. Response from Weather Bot.
4. Message sent to Weather Bot.
5. Response from Weather Bot (end of conversation with Weather Bot).
6. Message sent to Agent Bot.
7. Message redirected to Travel Bot.
8. Response from Travel Bot.
9. Message sent to Travel Bot.
10. Response from Travel Bot.
11. Message sent to Travel Bot.
12. Response from Travel Bot (end of conversation with Travel Bot).
13. Message sent to Agent Bot.
14. Response from Agent Bot (end of conversation).

## 6. Improving the Bots
Currently, each bot is configured to use keywords in order to determine a user's meaning. While this approach makes it easy to get started, it doesn't take full advantage of the machine learning capabilities of Watson Assistant. As a result, the bots won't be able to infer meaning if none of the keywords exist in the user's input. This could also lead to some unintended results!
### 6.1 Test agentBot
- Go to the Watson Assistant `Skills` tab and click on `agentBot`
![SkillsTabAgentBot](images/SkillsTabAgentBot.png)
- To test **agentBot* click on the `Try it` button in the upper right hand corner of the screen.
![AgentBotTryIt](images/AgentBotTryIt.png)
- You can use the `Try it` panel to send messages to `agentBot` and see what `intents` and `entities` are recognized. `Intents` are sentence level classifications while `Entitites` are phrase level classifications.
- Send the sentence: "book a cab".  `agentBot` will recognize the `#travel` intent. When `agentBot` finds the `#travel` intent, it signals a transfer to `travelBot`.
![AgentBotBookFlight](images/agent_bot_book_cab.png)
- Send the sentence: "I want a cab". `agentBot` will not recognize the `#travel` intent, because the `#travel` intent is trained on keywords relating to travel.
![AgentBotWantFlight](images/agent_bot_i_want_cab.png)
- Send the sentence: "get". `agentBot` recognizes the `#travel` intent, even though `get` is NOT part of our examples for `#travel`.  
![AgentBotGet](images/agent_bot_get.png)
### 6.2 Improve agentBot
- Within the `agentBot` skill, click the `Intents` tab and click the `#travel` intent to open up `intent details`.
![AgentBotTravelIntent](images/agent_bot_travel_intent.png)
- Within the `intent details` for the `#travel` intent, we can see that the `user examples`(training data) doesn't include mentions of `flight`, `cab`, or `taxi`. Since the current set of user examples are all keywords, the underlying model is weighted towards short phrases and the specific set of keywords. Since flight isn't listed as an example, "I want a flight" wasn't classified as `#travel`. This could also explain why "get" was incorrectly classified as `#travel`.  
![AgentBotTravelIntentDetails](images/agent_bot_travel_details.png)
- Add the following examples to the `#travel` intent of the `agentBot` skill under `Add user examples`. Since `agentBot` serves as a proxy to `travelBot`, keyword intents will suffice.
  1. `cab`
  2. `taxi`
  3. `flight`
- As you add examples, the `Try it out` panel will update to show that your workspace training is being updated  
![BotTraining](images/wa_training.png)
- Once the `Watson is training` notification has disappeared, use the `Try it out` panel to send the sentences: `I want a cab` and `Get me a flight`. Both sentences should be correctly classified as `#travel`.
### 6.3 Teach travelBot to Fly
Now that `agentBot` is better able to handle requests for travel, we need to ensure that `travelBot` can handle flight requests.
- From the Watson Assistant `Skills` menu, select the `travelBot` skill.
- Click on the `Dialog` tab of the `travelBot` skill to access the logic of `travelBot`. 
- Using the `Try it out` panel, send the message: "I want to book a flight". `travelBot` should classify the message as `#reserve` and should identify the entity of `@mode_of_transportation:flight`. 
- We will duplicate the the `Book Cab` node to enable `travelBot` to arrange flight reservations. Click the ![TripleDotMenu](images/triple_dot.png) on the `Book Cab` dialog node then click `Duplicate`.  
![DuplicateBookCab](images/travel_bot_duplicate_book_cab.png)
- The resulting node will be called `Book Cab - copy1`
![TravelBotBookCabCopy](images/travel_bot_book_cab_copy1.png)
- Click on the `Book Cab - copy` node and change it's name from `Book Cab - copy` to `Book Flight`. To ensure that the node is activated when users ask about reserving flights, change its trigger condition from `#reserve and @mode_of_transportation:cab` to `#reserve and @mode_of_transportation:flight`. This set of conditions will ensure that the node triggers whenever the `#reserve` intent is detected alongside the `@mode_of_transportation` entity with a value of `flight`.  
![TravelBotBookFlight](images/travel_bot_book_flight.png)
- Update the `If not present, ask` prompts for `$cabRequiredDate` and `$cabRequiredTime` to mention flight instead of cab. Update variable names from `$cabRequiredDate` and `$cabRequiredTime` to `$flightRequiredDate` and `$flightRequiredTime`
- Update the `Book Flight` node's response to `Your flight has been booked for $flightRequiredDate and you will be picked up at $flightRequiredTime`. 
![TravelBotBookFlightFinal](images/travel_bot_final_book_flight.png)
- Use the try it out panel to request a flight reservation. You will need to provide a date and a time for your reservation.   
![TravelBotBookFlightExample](images/travel_bot_book_flight_example.png)
# Plug and play process for a new bot

1. To add a new bot, create a new bot in the Watson Assistant service instance created earlier for this code pattern or you can use an existing bot that you want to use. Let's say you added a bot for `Restaurant Booking` and named it as `RESTAURANT_BOOKING`.
2. In `manifest.yml` file, add an entry for new bot as shown below
```
WORKSPACE_ID_RESTAURANT_BOOKING: xxxxxxxxxxxxxxxxxxxxxxxx
```
3. In the RESTAURANT_BOOKING Bot, when the conversation is over (last node in a dialog), add a context parameter `destination_bot` and value as `AGENT`. It enables the control to be passed to back to the Agent Bot. You can refer to the leaf nodes in other already imported bots for examples.
4. Open Agent Bot. Add an intent for Restaurant Booking, say restaurant. Then in dialog, add a node for restaurant intent. Add a context parameter `destination_bot` and value as `RESTAURANT_BOOKING`.
5. Redeploy the application for the configuration changes to take effect.
6. That's it, you are set to you new `RESTAURANT_BOOKING` bot as a plug and play bot.


# Summary
We introduced an Agent Bot which understands intents of messages. Agent Bot will redirect a message to a specific bot which can handle that message. We saw how to configure Agent Bot, Specific Bots and orchestration application to have this arrangement possible. We also saw how to plug and play this setup to add a new bot.


# Troubleshooting

* The application page displays but there is no greetings message, when the application pages loads.
  * Verify Watson Assistant username and password are correct.
  * Verify workspace_id of the agent bot is correct.

* No response for a chat message
Verify workspace_id of a specific bots is correct.

* If there is any other issue, check application logs using the below command
```
ibmcloud cf logs <app-name> --recent
```


# License
[Apache 2.0](LICENSE)
