

# Build a Chatbot with Amazon Lex

**Project Link:** [View Project](http://nextwork.ai/projects/aws-ai-lex1)

**Author:** phadagi mannda raven  
**Email:** ecommercesraven@gmail.com

---

## Build a Chatbot with Amazon Lex

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-ai-lex1_505be5b8)

---

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate how to create a chartbot Amazon lex. I'm doing this project to learn how to use the AWS  console and Amazon lex and practice creating real-World projects 

### What is Amazon Lex?

Amazon Lex is an AWS service that helps us build chatbot very easily. 

### Key tools and concepts

Services I used were Amazon lex . Key concepts I learnt include intents, utterances, fallback intent, and cofidence score threshold.

### Personal reflections

One thing I didn't expect in this project was how easy it is to create a chatbot without code.

### Project timeline

This project took me approximately one hour to complet  The most challenging part was deciding which voice to go with because they were all so much fun! It was most rewarding to my ot in action and respond in a way that i wanted!

---

## Setting up a Lex chatbot

### Approach to chatbot setup

In this step, I will create a new chartot from scratch  because Amazon lex gives me to customi a BankerBot 

### Chatbot creation process

I created my chatbot from scratch with Amazon Lex. Setting it up took me 7 minutes.

### IAM role configuration

While creating my chatbot, I also created a role with basic permissions because Amazon lex needs permision to ineract with other AWS services like lamdba.

### Intent classification confidence score

In terms of the intent classification confidence score, I kept the default value of 0.60. This means my chartbot needs to be at least 60% confident to respond to a user's input 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-ai-lex1_97dc2351)

---

## Intents

In this step, I will train my bot to greet user's  because i want a friendly experience for my customers.

### Creating my first intent

Intents are use goals that the chatbot needs to understand and respond to.

I created my first intent, WelcomeIntent, to greet  users when they say hello or ask for help

### Testing chatbot responses

I launched and tested my chatbot, which could respond successfully if I entered "Hiya," and "Hello,. 

---

## Handling unrecognized inputs

My chatbot returned the error message 'Intent FallbackIntent is fulfilled' when I entered " how are you , ello , helo "  This error message occurred because my bot couldn't match it to the initially defined intents.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-ai-lex1_505be5b8)

---

## Configuring FallbackIntent

In this step, I will customize the fallbackintent because i would like my chatbot to be friendly.

### When FallbackIntent triggers

FallbackIntent is a default intent in every chatbot that gets triggered when the htbot that gets triggered when the chatbot doesn't recognize the user's input.

I wanted to configure FallbackIntent because i want my BankerBot to respond to the user n a friendly manner.

### My FallbackIntent configuration

To configure FallbackIntent, I, customized the closing response messagd and add variations o it sounds more natural.

I also added variations! What this means for an end user is that they will see slightly different responses each time the bot doesn't understand.

---

## FallbackIntent with Variations

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-ai-lex1_c4fc89af)

---

## Initial Responses

In this project extension I'm about to spice up he Fallbackintent with an initial response and add more variation to the initial response . I'm doing this so that to be more familiar with Amazon Lex and making an awesome chtbot.

### Setting up initial responses

As an extension for this project, I'm setting up initial responses, which are " Hmm this is interesting.., and One moment.. " I've set up initial responses for a little humanized experience when e chatbot recogizes the intent.

### Initial response implementation

The initial response messages I set up are. "Hmm this is interesting " For the user, this means that there is more of conversational experience. 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-ai-lex1_09bcb9701)

---

---
