import requests
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
import time
import csv


def get_source_html(url):
    s = Service('C:/Users/7887346/PycharmProjects/pythonProject8/chromedriver.exe')
    driver = webdriver.Chrome(service=s)
    driver.maximize_window()

    try:
        driver.get(url)
        time.sleep(2)
        sections_count = 0

        while True:
            if driver.find_element(By.XPATH, '//button[contains(text(), "Показать еще")]'):
                with open("C:/Users/7887346/PycharmProjects/pythonProject8/page.html", "w+", encoding="utf-8") as file:
                    file.write(driver.page_source)
                    sections = driver.page_source.count('<section class="main grid">')
                    if sections == sections_count:
                        break
                    driver.execute_script("window.scrollTo(0, document.body.scrollHeight*2);")
                    time.sleep(3)
                    button = driver.find_element(By.XPATH, '//button[contains(text(), "Показать еще")]')
                    time.sleep(1)
                    driver.execute_script("arguments[0].click();", button)
                    sections_count += 1

    except Exception as ex:
        print(ex)

    finally:
        driver.close()
        driver.quit()


def get_item_urls(file1, file2):
    with open(file1, encoding="utf-8") as f:
        src = f.read()
    soup = BeautifulSoup(src, features="html.parser")
    items = soup.findAll('a', class_='uho__link uho__link--overlay')
    with open(file2, 'w', encoding="utf-8") as file:
        for item in items:
            file.write(f'https://www.kommersant.ru{item.get("href")}\n')


def parse(file1, file2):
    print('Обработка...')
    with open(file1, encoding="utf-8") as f:
        urls = f.readlines()
    with open(file2, 'w', encoding='utf-8-sig') as csvfile:  #
        file_writer = csv.writer(csvfile, delimiter=" ")
        # file_writer.writerow(["Ссылка", "Заголовок", "Описание", "Время"])
        for url in urls:
            url = url.strip()
            page = requests.get(url)
            if page.status_code != 200:
                print('Что-то с сайтом ', url)
                pass

            else:
                soup = BeautifulSoup(page.text, "html.parser")
                heading = soup.find('h1', class_="doc_header__name")
                description = soup.find('p', class_="doc__text")
                time = soup.find('time', class_="doc_header__publish_time")
                try:
                    file_writer.writerow([url, (heading.getText().replace(" ", " ")).strip(),
                                          (description.getText().replace(" ", " ")).strip(),
                                          time.getText().strip()])
                    # print(f'{url}\n{heading.getText().strip()}\n{description.getText().strip()}\n{time.getText().strip()}\n\n')
                except Exception as ex:
                    # print(ex)
                    # print(url)
                    pass


def main():
    date = input('Введите дату в формате ГГ-ММ-ДД ').replace(' ', '-')
    get_source_html(f'https://www.kommersant.ru/archive/region/77/day/{date}')
    file1 = 'C:/Users/7887346/PycharmProjects/pythonProject8/page.html'
    file2 = 'C:/Users/7887346/PycharmProjects/pythonProject8/urls.txt'
    file3 = 'C:/Users/7887346/PycharmProjects/pythonProject8/news.csv'
    get_item_urls(file1, file2)
    parse(file2, file3)


if __name__ == '__main__':
    main()
