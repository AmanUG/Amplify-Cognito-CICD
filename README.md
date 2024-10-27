# Building a React App with Amplify (Gen 1), Cognito, and CI/CD with GitHub

![image](https://github.com/user-attachments/assets/04118a57-234c-496d-abaa-85228c2a63e3)

## prerequisites

1 - AWS Account

2 - Code editor - IDE (example - VSCode)

3 - Installed on your environment: Nodejs and npm (https://nodejs.org/en/download/package-manager)

4 - Familiarity with AWS Management Console and IAM

5 – Github account

### Pricing
Generally generous free tier on both Amazon Cognito and AWS Amplify (we will use both services)
See pricing below
AWS Amplify 
https://aws.amazon.com/amplify/pricing/
![image](https://github.com/user-attachments/assets/ec466d90-907f-48cd-8a84-c2172c909fe5)

Amazon Congnito (MAUs = Monthly Active Users) 
https://aws.amazon.com/cognito/pricing/
![image](https://github.com/user-attachments/assets/a0a08b59-41d7-44e0-a819-2b9a559e66ee)

### 1 – Set up environment 
1.1	– Install AWS Amplify CLI 

On your Terminal issue the below command and press Enter

 `npm install -g @aws-amplify/cli`
 
<sub>The AWS Amplify CLI is a command-line tool that enables you to set up and manage the backend resources for web and mobile applications directly from your local environment. With the CLI, you can easily configure and deploy various AWS services to support their application’s backend, without needing to go into the AWS Management Console</sub>

1.2	– Configure Amplify

On your Terminal issue the below command and press Enter

`amplify configure`

<sub>The amplify configure command in AWS Amplify CLI is used to set up your AWS credentials so that the Amplify CLI can access your AWS account to manage resources.  It connects your local CLI environment to your AWS account.</sub>

![image](https://github.com/user-attachments/assets/d474a321-3d1f-4ca6-9c20-0ee24609507e)

This will launch a browser to the AWS Console. (Sign in to AWS Console with Administrator)

On your terminal press Enter and choose your AWS Region (up and down arrow keys). 

An AWS Amplify documentation will open and follow the steps to create IAM user with programmatic Access (Access Key ID and Secrete Access Key)
https://docs.amplify.aws/gen1/react/prev/start/project-setup/prerequisites/

Once you created the IAM User, return to your terminal and press Enter.

Enter Access Key ID, Secrete Access Key and provide a Profile Name for your local machine.

![image](https://github.com/user-attachments/assets/f4a57ed9-e965-4c8b-8211-68a8502eb49d)


### 2 – Create the React App

On your Terminal issue the below command and press Enter

`npx create-react-app <name of your app>` (I am calling mine my-quiz-app)

You will have your project directories and files on your IDE.

![image](https://github.com/user-attachments/assets/22164109-4dad-4e92-8a5e-3be222f05363)

Change to the root director of your APP (in my case my-quiz-app)

`cd <your app name>`

Now let’s initialize the Amplify Project within the React App. (We will get a series of questions here).
Make sure you are in the correct directory and issue the following command on your Terminal

`amplify init`

Answer questions like name of your App in AWS cloud

Next press y to go with the default configuration

Press Enter for the next prompts 

Enter “no” for “Help Improve Amplify…” to save time for this project 


### 3	– Add Authentication with Cognito

Go the AWS Console and navigate to AWS Amplify. Choose the name of your app (Webapp in my case (I went with the default suggestion) and click on your App.
![image](https://github.com/user-attachments/assets/0e95bf0f-fb6d-4c68-8c1a-772a1f98dd89)

An environment has been created for us (dev in my case)
![image](https://github.com/user-attachments/assets/80603d83-a6d5-429b-bb00-0fa06868d17e)

Go back to your terminal and issue the below command to add authentication.

`amplify add auth`

You will get prompted for authentication and security configuration – choose “Default Configuration” and press Enter. This option will give us Cognito.
![image](https://github.com/user-attachments/assets/81b5e4d3-20e9-44c6-a5e5-a81b994ecc7a)

Next, you will be prompted how you want your users to sign in – “Choose email” and press enter.

Next choose “no I am done” and press Enter.
![image](https://github.com/user-attachments/assets/e274ca6b-ca7f-4a1f-b7cb-9b3531c82806)

Next – Let’s push all the configurations to AWS by issuing the below command on your Terminal.

`amplify push`

Type “y”  to continue… 

*** Wait while all the configurations are pushed to AWS and Cognito User Pool is being created for us***

Go to the AWS console and confirm that a new Cognito User Pool has been created.

Now let's add a code to our App.js file under Src directory. This will enable us to create user and login to the webapp. (We have commented out the quiz capability on line 20 and line 33). Copy the entire command below and paste it on App.js

    import React from 'react';

    import './App.css';

    import '@aws-amplify/ui-react/styles.css';

    import awsExports from './aws-exports';

    import Quiz from './Quiz';

    Amplify.configure(awsExports);

    function App() {

      return (
  
        <div className="App">
    
          <Authenticator>
      
            {({ signOut }) => (
        
              <main>
          
                <header className='App-header'>
            
                  {/* Quiz Component */}
              
                  <Quiz />*/
              
                  {/* Sign Out Button */}
              
                  <button 
              
                    onClick={signOut} 
                
                    style={{ 
                
                      margin: '20px', 
                  
                      fontSize: '0.8rem', 
                  
                      padding: '5px 10px', 
                  
                      marginTop: '20px'
                  
                    }}
                
                  >
              
                    Sign Out
                
                  </button>
              
                </header>
            
              </main>
          
            )}
        
          </Authenticator>
      
        </div>
    
      );
  
    }

    export default withAuthenticator(App);

Now let’s install the Amplify Libraries that will give us the login interface. Issue the following command to your terminal and press enter.

`npm install aws-amplify @aws-amplify/ui-react`

Now let’s start the app by typing the following command on the terminal.

`npm start`

A sign-in page will be launched for us on local-host port 3000
![image](https://github.com/user-attachments/assets/e3d176c3-194f-409f-b58c-a0ad0a25d4f9)

Create an account and sign in. You will need to use a valid email address to get verification code.

Because we have commented out the Quiz capabilities we will only get a sign out button after logging in. This is what is expected. 

In Cognito user-pool, you will be able to see that a user has been created for you. 


### 4	- Add functionality and styling for the Quiz

Under the Src directory, create a new file and name it “Quiz.js”

Paste the below code in to the file you just created. 

    import React, { useState } from 'react';
    import quizData from './quizData';

    function Quiz() {
      const [currentQuestion, setCurrentQuestion] = useState(0);
      const [score, setScore] = useState(0);
      const [showScore, setShowScore] = useState(false);
      const [selectedAnswer, setSelectedAnswer] = useState(""); 
      const [isCorrect, setIsCorrect] = useState(null);

      const handleAnswerOptionClick = (option) => {
        const correctAnswer = quizData[currentQuestion].answer;
        setSelectedAnswer(option);
        if (option === correctAnswer) {
          setScore(score + 1);
          setIsCorrect(true);
        } else {
          setIsCorrect(false);
        }

        // Delay moving to the next question to allow the user to see feedback
        setTimeout(() => {
          const nextQuestion = currentQuestion + 1;
          if (nextQuestion < quizData.length) {
            setCurrentQuestion(nextQuestion);
            setIsCorrect(null); // Reset for the next question
            setSelectedAnswer(""); // Reset selected answer
          } else {
            setShowScore(true);
          }
        }, 1000); // Adjust time as needed
      };

      return (
        <div className='quiz'>
          {showScore ? (
            <div className='score-section'>
              You scored {score} out of {quizData.length}
            </div>
          ) : (
            <>
              <div className='question-section'>
                <div className='question-count'>
                  <span>Question {currentQuestion + 1}</span>/{quizData.length}
                </div>
                <div className='question-text'>{quizData[currentQuestion].question}</div>
              </div>
              <div className='answer-section'>
                {quizData[currentQuestion].options.map((option) => (
                  <button 
                    onClick={() => handleAnswerOptionClick(option)} 
                    key={option}
                    style={{ backgroundColor: selectedAnswer === option ? (isCorrect ? 'lightgreen' : 'pink') : '' }}
                  >
                    {option}
                  </button>
                ))}
              </div>
              {selectedAnswer && (
                <div style={{ marginTop: '10px' }}>
                  {isCorrect ? 'Correct! 🎉' : 'Sorry, that’s not right. 😢'}
                </div>
              )}
            </>
          )}
        </div>
      );
    }

    export default Quiz;

Save the file.

Create a second file on Src directory and name it “quizdata.js”

Paste the below commands to the file (quizData.js) and save the file.

    const quizData = [
        {
          question: "What was the first video game ever made?",
          options: ["Pong", "Spacewar!", "Tetris", "Computer Space"],
          answer: "Spacewar!"
        },
        {
          question: "Which company developed the first commercial antivirus software?",
          options: ["Symantec", "McAfee", "Norton", "Kaspersky Lab"],
          answer: "McAfee"
        },
        {
          question: "Which animal is featured in the official PHP logo?",
          options: ["Elephant", "Hippo", "Giraffe", "Lion"],
          answer: "Elephant"
        },
        {
          question: "What does 'HTTP' stand for?",
          options: ["HyperText Transfer Protocol", "Hyperlink Transfer Technology Protocol", "Hyperlink Text Transfer Protocol", "HyperText Technology Protocol"],
          answer: "HyperText Transfer Protocol"
        },
        {
          question: "Which programming language is known as the backbone of the World Wide Web?",
          options: ["Java", "C#", "Python", "HTML"],
          answer: "HTML"
        },
        {
          question: "What is the name of the world's first computer programmer?",
          options: ["Charles Babbage", "Ada Lovelace", "Alan Turing", "Grace Hopper"],
          answer: "Ada Lovelace"
        },
        {
          question: "In what year was the iPhone first introduced?",
          options: ["2005", "2007", "2009", "2011"],
          answer: "2007"
        },
        {
          question: "What was Google's original name?",
          options: ["BackRub", "Googol", "SearchMaster", "WebSearch"],
          answer: "BackRub"
        },
        {
          question: "Which of these companies was not founded in a garage?",
          options: ["Amazon", "Google", "Apple", "Microsoft"],
          answer: "Amazon"
        },
        {
          question: "What does 'GPU' stand for?",
          options: ["Graphical Processing Unit", "Graphics Performance Unit", "Graphics Processing Unit", "Graphical Performance Unit"],
          answer: "Graphics Processing Unit"
        },  
        {
          question: "What is the capital of France?",
          options: ["New York", "London", "Paris", "Dublin"],
          answer: "Paris"
        },
        {
          question: "Who painted the Mona Lisa?",
          options: ["Vincent Van Gogh", "Leonardo da Vinci", "Pablo Picasso", "Claude Monet"],
          answer: "Leonardo da Vinci"
        },
        {
          question: "What is the largest planet in our solar system?",
          options: ["Earth", "Jupiter", "Saturn", "Mars"],
          answer: "Jupiter"
        }
      ];
      
      export default quizData;

Go back to the App.js file and uncomment line 20 by removing the // and uncomment line 33 by removing {/*___*/} and save the file

![image](https://github.com/user-attachments/assets/27fca02d-9961-4aa0-ba69-7dced65634a1)

Note – Up to this point we are running our frontend from local host. Now it’s time to push all the code to Github and create a CI/CD. 

### 5	– Push Local Code to Github

Create a repository in your Github – name it whatever you want.

On your terminal issue the following commands to push your code to Github

Make sure you are at the root of your project cd <Your Project folder>

`git init`
    
`git add .`
    
`git commit -m “First commit”`
    
`git branch -M main`
    
`git remote add origin  https://github.com/<YOUR GITHUB ACCOUNT NAME>/<YOUR REPO>`

<sub>***When you create your repository, it will display the required commands***</sub>

`git push -u origin main`

Now your local code must be on Github repository you created. 

### 6	– Host the Frontend in Amplify Via GitHub (CI/CD) 

Navigate to AWS Amplify from the AWS Console. On the Branches tab choose “Get Started”

![image](https://github.com/user-attachments/assets/b51cedd3-34b9-41a1-9041-82e9f7290a6f)

Choose GitHub and click “Next”

Click on the search bar and find the repository you created for this project. If you are unable to find it, “Update GitHub permissions”. A new GitHub browser will popup and add the repository.

Click Next.

Scroll down and find “Select a backend environment to use with this branch” 

Select the backend App Name and choose the correct Environment. 


![image](https://github.com/user-attachments/assets/5c098a98-452c-472f-9f4e-a6e7673a1531)

Make sure “Full-stack deploys” is enabled

Amplify will need a Service Role to host our App. Create a new role if you don’t already have one. 

(AWS Service Role for Amplify) and choose that role. (amplifyconsole-backend-role). 

Click “Next” and “Save and Deploy”.

Wait for your App to be deployed on Amplify. 

Once done, click the URL Domain to visit your App.

![image](https://github.com/user-attachments/assets/503d31de-e16f-4be0-aec6-d8babef4ede1)

To confirm CICD is working, go to your GitHub repo and update quizData.js (edit in place) under the Src directory by removing the first question, commit changes and check your App.

![image](https://github.com/user-attachments/assets/67257a9a-c287-4411-8abc-e4a6ef5e8148)

Amplify will detect the changes and moves to building and deploying.

### 7	– Clean up

****Follow Instructions to cleanup everything****

![image](https://github.com/user-attachments/assets/95dbfa1a-5191-4633-867c-ec4efacdef4b)

Confirm by typing “delete” on the dialog box.

Once you delete the Amplify backend, Cognito should be deleted as well. 

You can delete your IAM user as well. 

____________________________________END____________________________________________









