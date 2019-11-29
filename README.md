# Build chatbot with QnA Maker and Azure Bot Service

In this workshop, you will learn how :
 * Create a QnA Maker knowledge base
 * Publish a QnA Maker knowledge base
 * Integrate a knowledge base with Azure Bot Service
 * Chat with the bot to verify the code is working
 * Add Chit-chat personality at your knowledge base
  
## Pre-requisites

* Azure Pass or subscription - [Try it for Free](https://azure.microsoft.com/en-gb/)

## Case description

A bot is an autonomous program that interacts with people or computer systems in a predictable way. The logic behind a bot is designed and programmed by the bot creator, often with the help of tools like the Microsoft Bot Framework. Microsoft also has other AI services, included in Azure Cognitive Services, that can enhance the capabilities of your bots.

QnA Maker is one of the AI services that can help a bot answer your users' natural language questions by matching them with the best possible answers from your knowledge base.

## What is the QnA Maker

QnA Maker is a cloud-based Natural Language Processing (NLP) service that easily creates a natural conversational layer over your data. It can be used to find the most appropriate answer for any given natural language input, from your custom knowledge base (KB) of information.

A client application for QnA Maker is any conversational application that communicates with a user in natural language to answer a question. Examples of client applications include social media apps, chat bots, and speech-enabled desktop applications.

## Create a knowledge base from QnA Maker Portal

### Create a knowledge base 

1. Sign in to the [QnA Maker](https://www.qnamaker.ai) portal. 

1. Select **Create a knowledge base** from the top menu.

    ![Screenshot of QnA Maker portal](media/create-kb-step-1.png)

1. Select Create a QnA service. You will be redirect on Azure Portal. 

1. Provide the required information:

Name: chatbot-qna-<your initials>
Subscription : Select your existing subscription
Pricing tier: Select F0 (3 Calls per second)
Resource group: Clic on Create new and type the name qna-lab-rg
Resource group location : Select Canada Central
Search pricing tier: Select F (3 Indexes)
Search Location:Select Canada Central
App name: chatbot-qna-<your initials>
Website Location:Select Canada Central
Application Insights Location: Select Canada Central

    ![Screenshot of QnA Maker portal](media/create-qna-service.png)

1. Click Create to deploy the service. This step might take a few moments.

1. Once the deployment is complete, return to QnA Maker portal and click Refresh on step 2.

1. Select your existing settings:  

    |Setting|Purpose|
    |--|--|
    |Microsoft Azure Directory ID|This ID is associated with the account you use to sign into the Azure portal and the QnA Maker portal. |
    |Azure Subscription name|The billing account in which you created the QnA Maker resource.|
    |Azure QnA Service|Your existing QnA Maker resource.|

    ![Screenshot of QnA Maker portal](media/create-kb-step-2.png)

1. Enter your knowledge base name, `AI Bootcamp kb`.

    ![Screenshot of QnA Maker portal](media/create-kb-step-3.png)

1. Populate your knowledge base with the following settings:  

    |Setting name|Setting value|Purpose|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |The contents of the FAQ at that URL are formatted with a question followed by an answer. QnA Maker can interpret this format to extract questions and the associated answers.|
    |File |_not used in this tutorial_|This uploads files for questions and answers. |
    |Chit-chat personality|None|This gives a friendly and casual [personality](Concepts/best-practices.md#chit-chat) to common questions and answers. You can edit these questions and answers later. |

    ![Screenshot of QnA Maker portal](media/create-kb-step-4.png)

1. Select **Create your KB** to finish the creation process.

    ![Screenshot of QnA Maker portal](media/create-kb-step-5.png)

### Review, save, and train the knowledge base

1. Review the questions and answers.

    ![Screenshot of QnA Maker portal](media/save-and-train-kb.png)

1. Select **Save and train** in the top menu bar.

### Publish to get knowledge base endpoints

Select the **Publish** button from the top menu. On the publish page, select **Publish**.

![Screenshot of QnA Maker portal](media/publish-1.png)

After the knowledge base is published, the endpoint is displayed.

![Screenshot of endpoint settings](media/publish-2.png)

Don't close this **Publish** page. You need it later in the tutorial, to create a bot. 

### Use cURL to query for an FAQ answer

1. Select the **Curl** tab. 

![Screenshot of Curl tab](media/publish-3-curl.png)

1. Copy the text of the **Curl** tab, and run it in a cURL-enabled terminal or command-line. The authorization header's value includes the text `Endpoint`, with a trailing space and then the key.

1. Replace `<Your question>` with `How large can my KB be?`. This is close to the question, `How large a knowledge base can I create?`, but not exactly the same. QnA Maker applies natural language processing to determine that the two questions are the same.     

1. Run the cURL command and receive the JSON response, including the score and answer. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 35.92,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker is somewhat confident with the score of 35.92%.  


### Use cURL to query for the default answer

Any question that QnA Maker is not confident about receives the default answer. This answer is configured in the Azure portal. 

1. In the cURL-enabled terminal, replace `How large can my KB be?` with `x`. 

1. Run the cURL command and receive the JSON response, including the score and answer. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker returned a score of `0`, which means no confidence. It also returned the default answer. 


## Add personnality with Chit-chat 

1. Select the last page of questions and answers from the bottom of the table. The page shows questions and answers from the Chit-chat personality. 

1. From the toolbar above the list of questions and answers, select the **View options** icon, and then select **Show metadata**. This shows the metadata tags for each question and answer. The Chit-chat questions have the **editorial: chit-chat** metadata already set. This metadata is returned to the client application, along with the selected answer. The client application, such as a chat bot, can use this filtered metadata to determine additional processing or interactions with the user.

    ![Screenshot of QnA Maker portal](media/save-and-train-kb-chit-chat.png)

### Use cURL to query for a Chit-chat answer


1. In the cURL-enabled terminal, replace `How large can my KB be?` with a bot conversation-ending statement from the user, such as `Thank you`.   

1. Run the cURL command and receive the JSON response, including the score and answer. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Because the question of `Thank you` exactly matched a Chit-chat question, QnA Maker is completely confident with the score of 100. QnA Maker also returned all the related questions, as well as the metadata property containing the Chit-chat metadata tag information.  

