{{#template name="tutorials.whatsapp2.ionic.step_06.md"}}

Our next step is about adding the ability to create new chats. So far we had the chats list and the users feature, we just need to connect them.

We will open the new chat view using Ionic's modal dialog. The dialog is gonna pop up from the chats view once we click on the icon at the top right corner of the view. Let's implement the handler in the chats component first:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.1"}}

And let's bind the event to the view:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.2"}}

The dialog should contain a list of all the users whom chat does not exist yet. Once we click on one of these users we should be demoted to the chats view with the new chat we've just created.

Since we wanna insert a new chat we need to create the corresponding method in the `methods.ts` file:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.3"}}

As you can see, a chat is inserted with an additional `memberIds` field. Let's update the chat model accordingly:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.4"}}

Now, in order to have access to the Meteor's Collection of Users, we need to wrap it as a `MeteorObservable` Collection:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.5"}}

> We take Meteor variable from `Package["meteor"].Meteor` because this file is loaded in both client and server, and this is the only way that works with both.

And let's create a TypeScript interface for User objects:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.6"}}

Now that we have the method ready we can go ahead and implement the new chat dialog:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.7"}}

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.8"}}

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.9"}}

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.10"}}

Thanks to our new-chat dialog, we can create chats dynamically with no need in initial fabrication. Let's replace the chats fabrication with users fabrication in the Meteor server:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.11"}}

And let's add the missing import inside the `ChatsPage`:

Since we changed the data fabrication method, the chat's title and picture are not hardcoded anymore, therefore they should be calculated in the components themselves. Let's calculate those fields in the chats component:

{{> DiffBox tutorialName="whatsapp2-ionic-tutorial" step="6.13"}}

Now we want our changes to take effect. We will reset the database so next time we run our Meteor server the users will be fabricated. To reset the database, first make sure the Meteor server is stopped and then type the following command:

    $ meteor reset

And once we start our server again it should go through the initialization method and fabricate the users.

The fabricated users should appear in the new-chat dialog like so:

> *android* {{tutorialImage 'whatsapp2' 'screenshot-8-md.png' 500}}

> *ios* {{tutorialImage 'whatsapp2' 'screenshot-8-ios.png' 500}}

{{/template}}