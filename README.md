# Speech-recognition
import requests
from bs4 import BeautifulSoup
import speech_recognition as sr


def google_search(query):

    url = f"https://www.google.com/search?q={query.replace(' ', '+')}"
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
    }


    response = requests.get(url, headers=headers)


    soup = BeautifulSoup(response.text, 'html.parser')


    results = []
    for item in soup.find_all('div', class_='g'):
        title = item.find('h3')
        link = item.find('a')['href']

        if title and link:
            results.append((title.get_text(), link))

    return results


def voice_search():

    recognizer = sr.Recognizer()

    with sr.Microphone() as source:
        print("Ovoz qidiring...")
        audio = recognizer.listen(source)
        print("Ovoz qabul qilindi.")

    try:

        query = recognizer.recognize_google(audio, language='uz-UZ')
        print(f"Qidiruv so'rovi: {query}")
        return query
    except sr.UnknownValueError:
        print("Ovoz aniqlanmadi. Iltimos, qaytadan harakat qiling.")
        return None
    except sr.RequestError as e:
        print(f"Google xizmatiga ulanishda xatolik: {e}")
        return None


user_input = voice_search()
if not user_input:
    user_input = input("Qidirish: ")

if user_input:
    results = google_search(user_input)
    print("Topilgan natijalar:")
    for title, link in results:
        print(f"Nom: {title}\nHavola: {link}\n")
