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
## Infrastructure as Code

### Overview

The following sections provide declarative infrastructure definitions for reproducing this Amazon Lex chatbot using **AWS CloudFormation** and **HashiCorp Terraform**. Both templates provision:

- An Amazon Lex V2 bot with the `WelcomeIntent`
- A customized `FallbackIntent` with response variations and initial responses
- An IAM role with the minimum required permissions
- A bot alias for runtime access

---

### CloudFormation (YAML)

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: >
  Amazon Lex V2 chatbot with WelcomeIntent, customized FallbackIntent,
  response variations, and initial responses.

Parameters:
  BotName:
    Type: String
    Default: BankerBot
    Description: Name of the Amazon Lex bot.
  BotAliasName:
    Type: String
    Default: prod
    Description: Name of the bot alias.
  ConfidenceThreshold:
    Type: Number
    Default: 0.60
    MinValue: 0.0
    MaxValue: 1.0
    Description: NLU intent classification confidence threshold.

Resources:
  # ------------------------------------------------------------------
  # IAM Role for Amazon Lex
  # ------------------------------------------------------------------
  LexBotRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: !Sub "${BotName}-lex-role"
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lexv2.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonLexRunBotsOnly
      Tags:
        - Key: Project
          Value: AmazonLexChatbot

  # ------------------------------------------------------------------
  # Amazon Lex V2 Bot
  # ------------------------------------------------------------------
  LexBot:
    Type: AWS::Lex::Bot
    Properties:
      Name: !Ref BotName
      RoleArn: !GetAtt LexBotRole.Arn
      DataPrivacy:
        ChildDirected: false
      IdleSessionTTLInSeconds: 300
      AutoBuildBotLocales: true
      BotLocales:
        - LocaleId: en_US
          NluConfidenceThreshold: !Ref ConfidenceThreshold
          VoiceSettings:
            VoiceId: Joanna
            Engine: standard
          Intents:
            # ----------------------------------------------------------
            # WelcomeIntent
            # ----------------------------------------------------------
            - Name: WelcomeIntent
              Description: Greets users when they initiate conversation.
              SampleUtterances:
                - Utterance: Hello
                - Utterance: Hiya
                - Utterance: Hi
                - Utterance: Hey
                - Utterance: Help
              FulfillmentCodeHook:
                Enabled: false
              ClosingResponse:
                MessageGroupsList:
                  - Message:
                      PlainTextMessage:
                        Value: >
                          Hello! Welcome to BankerBot. How can I assist you today?

            # ----------------------------------------------------------
            # FallbackIntent (Customized)
            # ----------------------------------------------------------
            - Name: FallbackIntent
              Description: >
                Triggered when user input cannot be matched to any
                defined intent above the confidence threshold.
              ParentIntentSignature: AMAZON.FallbackIntent
              InitialResponse:
                MessageGroupsList:
                  - Message:
                      PlainTextMessage:
                        Value: "Hmm, this is interesting..."
                  - Message:
                      PlainTextMessage:
                        Value: "One moment..."
                AllowInterrupt: true
              ClosingResponse:
                MessageGroupsList:
                  - Message:
                      PlainTextMessage:
                        Value: >
                          I'm sorry, I didn't quite catch that.
                          Could you rephrase or ask something else?
                  - Message:
                      PlainTextMessage:
                        Value: >
                          Hmm, I'm not sure I understood.
                          Can you try saying that differently?
                  - Message:
                      PlainTextMessage:
                        Value: >
                          I didn't get that. Let me know how I can help!
                AllowInterrupt: true

  # ------------------------------------------------------------------
  # Bot Version
  # ------------------------------------------------------------------
  LexBotVersion:
    Type: AWS::Lex::BotVersion
    Properties:
      BotId: !Ref LexBot
      BotVersionLocaleSpecification:
        - LocaleId: en_US
          BotVersionLocaleDetails:
            SourceBotVersion: DRAFT

  # ------------------------------------------------------------------
  # Bot Alias
  # ------------------------------------------------------------------
  LexBotAlias:
    Type: AWS::Lex::BotAlias
    Properties:
      BotId: !Ref LexBot
      BotAliasName: !Ref BotAliasName
      BotVersion: !GetAtt LexBotVersion.BotVersion
      BotAliasLocaleSettings:
        - LocaleId: en_US
          BotAliasLocaleSetting:
            Enabled: true

Outputs:
  BotId:
    Description: Unique identifier of the Lex bot
    Value: !Ref LexBot
  BotAliasId:
    Description: Unique identifier of the bot alias
    Value: !Ref LexBotAlias
  BotRoleArn:
    Description: ARN of the IAM role assigned to the bot
    Value: !GetAtt LexBotRole.Arn
```

**Deployment:**

```bash
aws cloudformation deploy \
  --template-file lex-chatbot.yaml \
  --stack-name lex-bankerbot-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

---

### Terraform (HCL)

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# ------------------------------------------------------------------
# Variables
# ------------------------------------------------------------------
variable "aws_region" {
  description = "AWS region for resource deployment"
  type        = string
  default     = "us-east-1"
}

variable "bot_name" {
  description = "Name of the Amazon Lex V2 bot"
  type        = string
  default     = "BankerBot"
}

variable "bot_alias_name" {
  description = "Name of the bot alias"
  type        = string
  default     = "prod"
}

variable "confidence_threshold" {
  description = "NLU intent classification confidence threshold"
  type        = number
  default     = 0.60
}

# ------------------------------------------------------------------
# Data Sources
# ------------------------------------------------------------------
data "aws_caller_identity" "current" {}
data "aws_partition" "current" {}

# ------------------------------------------------------------------
# IAM Role for Amazon Lex
# ------------------------------------------------------------------
resource "aws_iam_role" "lex_bot_role" {
  name = "${var.bot_name}-lex-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "lexv2.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })

  managed_policy_arns = [
    "arn:${data.aws_partition.current.partition}:iam::aws:policy/AmazonLexRunBotsOnly"
  ]

  tags = {
    Project = "AmazonLexChatbot"
  }
}

# ------------------------------------------------------------------
# Amazon Lex V2 Bot
# ------------------------------------------------------------------
resource "aws_lexv2models_bot" "banker_bot" {
  name        = var.bot_name
  description = "Conversational banking assistant built with Amazon Lex V2"
  role_arn    = aws_iam_role.lex_bot_role.arn
  data_privacy {
    child_directed = false
  }
  idle_session_ttl_in_seconds = 300
}

# ------------------------------------------------------------------
# Bot Locale (en_US)
# ------------------------------------------------------------------
resource "aws_lexv2models_bot_locale" "en_us" {
  bot_id                           = aws_lexv2models_bot.banker_bot.id
  bot_version                      = "DRAFT"
  locale_id                        = "en_US"
  n_lu_confidence_threshold        = var.confidence_threshold
  voice_settings {
    voice_id = "Joanna"
    engine   = "standard"
  }
}

# ------------------------------------------------------------------
# WelcomeIntent
# ------------------------------------------------------------------
resource "aws_lexv2models_intent" "welcome_intent" {
  bot_id      = aws_lexv2models_bot.banker_bot.id
  bot_version = aws_lexv2models_bot_locale.en_us.bot_version
  locale_id   = aws_lexv2models_bot_locale.en_us.locale_id
  name        = "WelcomeIntent"
  description = "Greets users when they initiate conversation."

  sample_utterance {
    utterance = "Hello"
  }
  sample_utterance {
    utterance = "Hiya"
  }
  sample_utterance {
    utterance = "Hi"
  }
  sample_utterance {
    utterance = "Hey"
  }
  sample_utterance {
    utterance = "Help"
  }

  closing_response {
    message_group {
      message {
        plain_text_message {
          value = "Hello! Welcome to BankerBot. How can I assist you today?"
        }
      }
    }
  }
}

# ------------------------------------------------------------------
# FallbackIntent (Customized)
# ------------------------------------------------------------------
resource "aws_lexv2models_intent" "fallback_intent" {
  bot_id      = aws_lexv2models_bot.banker_bot.id
  bot_version = aws_lexv2models_bot_locale.en_us.bot_version
  locale_id   = aws_lexv2models_bot_locale.en_us.locale_id
  name        = "FallbackIntent"
  description = "Triggered when user input cannot be matched to any defined intent."

  parent_intent_signature = "AMAZON.FallbackIntent"

  initial_response {
    message_group {
      message {
        plain_text_message {
          value = "Hmm, this is interesting..."
        }
      }
    }
    message_group {
      message {
        plain_text_message {
          value = "One moment..."
        }
      }
    }
    allow_interrupt = true
  }

  closing_response {
    message_group {
      message {
        plain_text_message {
          value = "I'm sorry, I didn't quite catch that. Could you rephrase or ask something else?"
        }
      }
    }
    message_group {
      message {
        plain_text_message {
          value = "Hmm, I'm not sure I understood. Can you try saying that differently?"
        }
      }
    }
    message_group {
      message {
        plain_text_message {
          value = "I didn't get that. Let me know how I can help!"
        }
      }
    }
    allow_interrupt = true
  }
}

# ------------------------------------------------------------------
# Bot Version
# ------------------------------------------------------------------
resource "aws_lexv2models_bot_version" "version_1" {
  bot_id = aws_lexv2models_bot.banker_bot.id
  locale_specification {
    locale_id = aws_lexv2models_bot_locale.en_us.locale_id
    source_bot_version_locale_details {
      bot_version = "DRAFT"
    }
  }
}

# ------------------------------------------------------------------
# Bot Alias
# ------------------------------------------------------------------
resource "aws_lexv2models_bot_alias" "prod_alias" {
  bot_id      = aws_lexv2models_bot.banker_bot.id
  bot_version = aws_lexv2models_bot_version.version_1.bot_version
  name        = var.bot_alias_name

  bot_alias_locale_setting {
    locale_id = aws_lexv2models_bot_locale.en_us.locale_id
    enabled   = true
  }
}

# ------------------------------------------------------------------
# Outputs
# ------------------------------------------------------------------
output "bot_id" {
  description = "Unique identifier of the Lex bot"
  value       = aws_lexv2models_bot.banker_bot.id
}

output "bot_alias_id" {
  description = "Unique identifier of the bot alias"
  value       = aws_lexv2models_bot_alias.prod_alias.id
}

output "bot_role_arn" {
  description = "ARN of the IAM role assigned to the bot"
  value       = aws_iam_role.lex_bot_role.arn
}
```

**Deployment:**

```bash
terraform init
terraform plan
terraform apply
```

---

## IaC Resource Mapping

| Manual Console Step | CloudFormation Resource | Terraform Resource |
|---------------------|------------------------|--------------------|
| Create bot | `AWS::Lex::Bot` | `aws_lexv2models_bot` |
| Configure locale (en_US) | `BotLocales` (nested) | `aws_lexv2models_bot_locale` |
| Define WelcomeIntent | `Intents` (nested) | `aws_lexv2models_intent` |
| Customize FallbackIntent | `Intents` (nested) | `aws_lexv2models_intent` |
| Build bot version | `AWS::Lex::BotVersion` | `aws_lexv2models_bot_version` |
| Create alias | `AWS::Lex::BotAlias` | `aws_lexv2models_bot_alias` |
| IAM role | `AWS::IAM::Role` | `aws_iam_role` |

---

## Summary

| Phase | Key Action | Outcome |
|-------|-----------|---------|
| Setup | Custom bot creation + IAM role provisioning | Functional Lex bot deployed |
| Intent Design | `WelcomeIntent` with utterance corpus | Successful greeting recognition |
| Error Handling | FallbackIntent customization | Improved UX for unrecognized input |
| Extension | Initial response + variation injection | Enhanced conversational naturalness |
| IaC | CloudFormation + Terraform templates | Reproducible, version-controlled infrastructure |

---

