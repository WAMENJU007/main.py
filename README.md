# main.py
import requests
import json
from prompts import CV_REVIEW_PROMPT  # <--- Step 1: Import your prompt

# AI API config
API_URL = "https://api.openai.com/v1/chat/completions"
API_KEY = "your-api-key" # This is my actual API key

def connect_to_ai(prompt):
    # Connect to AI API
    headers = {"Authorization": f"Bearer {API_KEY}", "Content-Type": "application/json"}
    data = {
        "model": "gpt-4o-mini", # Or "gpt-4" as you have in your image
        "messages": [{"role": "user", "content": prompt}],
        "max_tokens": 500
    }

    try:
        response = requests.post(API_URL, headers=headers, json=data)
        response.raise_for_status() 
        # Properly parsing the JSON response
        return response.json()['choices'][0]['message']['content']
    except requests.exceptions.RequestException as e:
        return f"Error connecting to AI API: {e}"

def handle_request(cv_text):
    # Step 2: Use the imported prompt and inject the CV text
    # This replaces the {cv_text} placeholder in prompts.py with the user's input
    full_prompt = CV_REVIEW_PROMPT.format(cv_text=cv_text)

    # Get AI response
    ai_response = connect_to_ai(full_prompt)
    return ai_response

def main():
    print("--- AI CV Reviewer Tool ---")
    # Instead of hardcoded text, let's use input() to meet the "User Input" requirement
    user_cv = input("Please paste the CV text you want to review: \n")
    
    if user_cv.strip():
        print("\nAnalyzing... Please wait.\n")
        feedback = handle_request(user_cv)
        print("CV Review Feedback:")
        print(feedback)
    else:
        print("No CV text provided. Exiting.")

if __name__ == "__main__":
    main()
