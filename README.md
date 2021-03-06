#FormAction SDK for Rasa


###Installation :
```
npm i nodejs-formaction-sdk-rasa
```
#####OR
```
npm i https://github.com/rohitnairtech/nodejs-formaction-sdk-rasa/tarball/master
```
###Usage:
```javascript
const {handleFormAction} = require('nodejs-formaction-sdk-rasa');
handleFormAction(<REQUIRED_SLOTS_ARRAY>, <ENTITIES_ARRAY>, <SLOT_ARRAY>, <TEMPLATE_ARRAY>, <NEXT_ACTION_NAME>, <senderID_OPTIONAL>);
```
#####OR
```javascript
const formAction = require('nodejs-formaction-sdk-rasa');
formAction.handleFormAction(<REQUIRED_SLOTS_ARRAY>, <ENTITIES_ARRAY>, <SLOT_ARRAY>, <TEMPLATE_ARRAY>, <NEXT_ACTION_NAME>, <senderID_OPTIONAL>);
```

###Example:
```javascript
const {handleFormAction} = require('nodejs-formaction-sdk-rasa');

const request = req.body; // from express endpoint in external nodejs actions endpoint
const required_slot = ['origin', 'destination', 'date'];
const entites = request.tracker.latest_message.entities;
const slots = request.tracker.slots;
const templates = request.domain.templates;
const nextAction = 'utter_flight_details';
const senderID = request.sender_id;
//SenderID is used to send out random unrepeating utterance if multiple utterance available. Optional feature to enhance user experience 

const formHandle = handleFormAction(required_slot, entities, slots, templates, nextAction, senderID);
formHandle.then(resp=>{
	console.log(resp);
	//send the response back to rasa python agent
}).catch(err=>{
	console.log(err);
});
```

###Example 2:
```javascript
"use strict";

const express = require('express');
const app = express();
const port = 3001;

app.use(express.json({limit: '5mb'}));

app.post('/actionWebhook', (req, res) => {
    const request = req.body, next_action = request.next_action, entities = request.tracker.latest_message.entities, slots = request.tracker.slots, sender = request.sender_id;
    
    let formAction;

    switch(next_action){
        case 'form_name':
            formAction = handleFormAction(['origin', 'destination', 'flight_class', 'num_people', 'date'], entities, slots, 'action_flight_details', sender);
            formAction.then(resp=>{
                res.json(resp);
            }).catch(err=>{
                console.log(err);
            });
            break;
    }

});

app.listen(port, () => console.log(`Actions server listening on port ${port}`));

```

NOTES:
- TURN OFF AUTOFILL for SLOTS in domain, but use the same name as the ENTITIES for SLOTS - SlotFilling happens in the module
- Register utter_ask_<SLOT_NAME> this is used to dynamically utter a question back to the user.
- Register nextAction name - utterance/custom action name in domain. That action will called upon successfully completing slot filling.