import pygame
import requests
import sys
import os
sp = ['skl', 'map', 'sat']
class Label:
    def __init__(self, rect, text):
        self.rect = pygame.Rect(rect)
        self.text = text
        self.bgcolor = pygame.Color("white")
        self.font_color = pygame.Color("red")
        # Рассчитываем размер шрифта в зависимости от высоты
        self.font = pygame.font.Font(None, self.rect.height - 8)
        self.rendered_text = None
        self.rendered_rect = None


    def render(self, surface):
        surface.fill(self.bgcolor, self.rect)
        self.rendered_text = self.font.render(self.text, 1, self.font_color)
        self.rendered_rect = self.rendered_text.get_rect(x=self.rect.x + 2, centery=self.rect.centery)
        # выводим текст
        surface.blit(self.rendered_text, self.rendered_rect)
class AdresBox(Label):
    def __init__(self, rect, text):
        super().__init__(rect, text)
        self.text = text
        self.active = False
        self.blink = True
        self.blink_timer = 0

    def get_event(self, event):
        if event.type == pygame.KEYDOWN and self.active:
            if event.key == pygame.K_KP_ENTER:
                self.active = False
            elif event.key == pygame.K_BACKSPACE:
                if len(self.text) > 0:
                    self.text = self.text[:-1]
            else:
                self.text += event.unicode

        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1 or event.type == pygame.KEYDOWN and event.key == pygame.K_TAB:
            self.active = True

    def update(self):
        if pygame.time.get_ticks() - self.blink_timer > 200:
            self.blink = not self.blink
            self.blink_timer = pygame.time.get_ticks()

    def render(self, surface):

        super(AdresBox, self).render(surface)
        if self.blink and self.active:
            pygame.draw.line(surface, pygame.Color("black"),
                             (self.rendered_rect.right + 2, self.rendered_rect.top + 2),
                             (self.rendered_rect.right + 2, self.rendered_rect.bottom - 2))
class GUI:
    def __init__(self):
        self.elements = []

    def add_element(self, element):
        self.elements.append(element)

    def render(self, surface):
        for element in self.elements:
            render = getattr(element, "render", None)
            if callable(render):
                element.render(surface)

    def update(self):
        for element in self.elements:
            update = getattr(element, "update", None)
            if callable(update):
                element.update()

    def get_event(self, event):
        for element in self.elements:
            get_event = getattr(element, "get_event", None)
            if callable(get_event):
                element.get_event(event)
class Button(Label):
    def __init__(self, rect, text):
        super().__init__(rect, text)
        self.bgcolor = pygame.Color("gray")
        # при создании кнопка не нажата
        self.pressed = False

    def render(self, surface):
        surface.fill(self.bgcolor, self.rect)
        self.rendered_text = self.font.render(self.text, 1, self.font_color)
        if not self.pressed:
            color1 = pygame.Color("white")
            color2 = pygame.Color("black")
            self.rendered_rect = self.rendered_text.get_rect(x=self.rect.x + 5, centery=self.rect.centery)
        else:
            color1 = pygame.Color("black")
            color2 = pygame.Color("white")
            self.rendered_rect = self.rendered_text.get_rect(x=self.rect.x + 7, centery=self.rect.centery + 2)

        # рисуем границу
        pygame.draw.rect(surface, color1, self.rect, 2)
        pygame.draw.line(surface, color2, (self.rect.right - 1, self.rect.top), (self.rect.right - 1, self.rect.bottom), 2)
        pygame.draw.line(surface, color2, (self.rect.left, self.rect.bottom - 1),
                         (self.rect.right, self.rect.bottom - 1), 2)
        # выводим текст
        surface.blit(self.rendered_text, self.rendered_rect)

    def get_event(self, event):
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            self.pressed = self.rect.collidepoint(event.pos)
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1:
            self.pressed = False
l = "22"
l_pro = 22
x_pro = 135
y_pro = -28
stat_pro = 'sat'
response = None
m = 0
try:
    map_request = "https://static-maps.yandex.ru/1.x/?ll="+str(x_pro)+","+str(y_pro)+"&spn="+l+","+l+"&l="+stat_pro
    response = requests.get(map_request)

    if not response:
        print("Ошибка выполнения запроса:")
        print(geocoder_request)
        print("Http статус:", response.status_code, "(", response.reason, ")")
        sys.exit(1)
except:
    print("Запрос не удалось выполнить. Проверьте наличие сети Интернет.")
    sys.exit(1)

# Запишем полученное изображение в файл.
map_file = "map.png"
try:
    with open(map_file, "wb") as file:
        file.write(response.content)
except IOError as ex:
    print("Ошибка записи временного файла:", ex)
    sys.exit(2)

# Инициализируем pygame
pygame.init()
sl1 = 300
sl2 = 225
screen = pygame.display.set_mode((600, 450))
# Рисуем картинку, загружаемую из только что созданного файла.
screen.blit(pygame.image.load(map_file), (0, 0))
# Переключаем экран и ждем закрытия окна.
pygame.display.flip()
gui = GUI()
knopka1 = Button((1, 1, 35, 35), "Sw")
bar1 = AdresBox((400,1 , 200, 20), "")
gui.add_element(bar1)
gui.add_element(knopka1)
mrt = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.MOUSEBUTTONDOWN:
            x, y = event.pos
        if event.type == pygame.MOUSEBUTTONUP and event.button == 1 and x <= 35 and y <= 35:
            stat_pro = sp[m]
            m += 1
            if m == 3:
                m = 0
            map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
            response = requests.get(map_request)
            map_file = "map.png"
            try:
                with open(map_file, "wb") as file:
                    file.write(response.content)
            except IOError as ex:
                print("Ошибка записи временного файла:", ex)
                sys.exit(2)
            screen = pygame.display.set_mode((600, 450))
            screen.blit(pygame.image.load(map_file), (0, 0))
            pygame.display.flip()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RETURN:
                geocoder_request = "http://geocode-maps.yandex.ru/1.x/?geocode=" + bar1.text + ", 1&format=json"
                try:
                    response = requests.get(geocoder_request)
                    if response:
                        json_response = response.json()
                        toponym = json_response["response"]["GeoObjectCollection"]["featureMember"][-1]["GeoObject"]
                        toponym_coodrinates = toponym["Point"]["pos"].split(' ')
                        x, y = float(toponym_coodrinates[0]), float(toponym_coodrinates[-1])
                        x_pro = x
                        y_pro = y
                        map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                            y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                        response = requests.get(map_request)
                        map_file = "map.png"
                        try:
                            with open(map_file, "wb") as file:
                                file.write(response.content)
                        except IOError as ex:
                            print("Ошибка записи временного файла:", ex)
                            sys.exit(2)
                        screen = pygame.display.set_mode((600, 450))
                        screen.blit(pygame.image.load(map_file), (0, 0))
                        pygame.draw.circle(screen, pygame.Color("red"), (sl1, sl2), 15, 0)
                        mrt = True
                        pygame.display.flip()
                    else:
                        print("Ошибка выполнения запроса:")
                        print(geocoder_request)
                        print("Http статус:", response.status_code, "(", response.reason, ")")
                except:
                    print("Запрос не удалось выполнить. Проверьте наличие сети Интернет.")
            if event.key == pygame.K_PAGEUP:
                if l_pro < 30:
                    l_pro += 3
                else:
                    pass
                l = str(l_pro)
                map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                    y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                response = requests.get(map_request)
                map_file = "map.png"
                try:
                    with open(map_file, "wb") as file:
                        file.write(response.content)
                except IOError as ex:
                    print("Ошибка записи временного файла:", ex)
                    sys.exit(2)
                screen = pygame.display.set_mode((600, 450))
                screen.blit(pygame.image.load(map_file), (0, 0))
                pygame.display.flip()
            if event.key == pygame.K_PAGEDOWN:
                if l_pro >= 3:
                    l_pro -= 3
                else:
                    pass
                l = str(l_pro)
                map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                    y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                response = requests.get(map_request)
                map_file = "map.png"
                try:
                    with open(map_file, "wb") as file:
                        file.write(response.content)
                except IOError as ex:
                    print("Ошибка записи временного файла:", ex)
                    sys.exit(2)
                screen = pygame.display.set_mode((600, 450))
                screen.blit(pygame.image.load(map_file), (0, 0))
                pygame.display.flip()
            if event.key == pygame.K_UP:
                if y_pro >= 71:
                    pass
                else:
                    y_pro += 3
                    sl2 += 17
                map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                    y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                response = requests.get(map_request)
                map_file = "map.png"
                try:
                    with open(map_file, "wb") as file:
                        file.write(response.content)
                except IOError as ex:
                    print("Ошибка записи временного файла:", ex)
                    sys.exit(2)
                screen = pygame.display.set_mode((600, 450))
                screen.blit(pygame.image.load(map_file), (0, 0))
                if mrt:
                    pygame.draw.circle(screen, pygame.Color("red"), (sl1, sl2), 15, 0)
                pygame.display.flip()
            if event.key == pygame.K_DOWN:
                if y_pro <= -69:
                    pass
                else:
                    y_pro -= 3
                    sl2 -= 17
                map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                    y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                response = requests.get(map_request)
                map_file = "map.png"
                try:
                    with open(map_file, "wb") as file:
                        file.write(response.content)
                except IOError as ex:
                    print("Ошибка записи временного файла:", ex)
                    sys.exit(2)
                screen = pygame.display.set_mode((600, 450))
                screen.blit(pygame.image.load(map_file), (0, 0))
                if mrt:
                    pygame.draw.circle(screen, pygame.Color("red"), (sl1, sl2), 15, 0)
                pygame.display.flip()
            if event.key == pygame.K_LEFT:
                if x_pro <= -66:
                    pass
                else:
                    x_pro -= 3
                    sl1 += 15
                map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                    y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                response = requests.get(map_request)
                map_file = "map.png"
                try:
                    with open(map_file, "wb") as file:
                        file.write(response.content)
                except IOError as ex:
                    print("Ошибка записи временного файла:", ex)
                    sys.exit(2)
                screen = pygame.display.set_mode((600, 450))
                screen.blit(pygame.image.load(map_file), (0, 0))
                if mrt:
                    pygame.draw.circle(screen, pygame.Color("red"), (sl1, sl2), 15, 0)
                pygame.display.flip()
            if event.key == pygame.K_RIGHT:
                if x_pro >= 175:
                    pass
                else:
                    x_pro += 3
                    sl1 -= 15
                map_request = "https://static-maps.yandex.ru/1.x/?ll=" + str(x_pro) + "," + str(
                    y_pro) + "&spn=" + l + "," + l + "&l=" + stat_pro
                response = requests.get(map_request)
                map_file = "map.png"
                try:
                    with open(map_file, "wb") as file:
                        file.write(response.content)
                except IOError as ex:
                    print("Ошибка записи временного файла:", ex)
                    sys.exit(2)
                screen = pygame.display.set_mode((600, 450))
                screen.blit(pygame.image.load(map_file), (0, 0))
                if mrt:
                    pygame.draw.circle(screen, pygame.Color("red"), (sl1, sl2), 15, 0)
                pygame.display.flip()
        gui.get_event(event)
    gui.render(screen)
    gui.update()
    pygame.display.flip()

pygame.quit()
os.remove(map_file)
