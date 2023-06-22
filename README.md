# quiz
import random
from builtins import enumerate, zip, len, range

from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.uix.togglebutton import ToggleButton
from kivy.clock import Clock

class MultiplicationQuiz(App):
    def build(self):
        self.title = "Multiplication Quiz"
        self.questions = []
        self.current_question = None
        self.correct_answers = 0
        self.total_questions = 0
        self.timer = None

        layout = BoxLayout(orientation='vertical', padding=10)
        self.question_label = Label(text="", size_hint=(1, 0.3))
        layout.add_widget(self.question_label)

        self.option_buttons = []
        for _ in range(4):
            button = ToggleButton(text="", size_hint=(1, 0.2))
            button.bind(on_release=self.select_option)
            self.option_buttons.append(button)
            layout.add_widget(button)

        self.answer_label = Label(text="", size_hint=(1, 0.2))
        layout.add_widget(self.answer_label)

        self.start_button = Button(text="Start Quiz", on_release=self.start_quiz, size_hint=(1, 0.1))
        layout.add_widget(self.start_button)

        self.next_button = Button(text="Next", on_release=self.next_question, size_hint=(1, 0.1))
        self.next_button.disabled = True
        layout.add_widget(self.next_button)

        self.submit_button = Button(text="Submit", on_release=self.check_answer, size_hint=(1, 0.1))
        self.submit_button.disabled = True
        layout.add_widget(self.submit_button)

        self.timer_label = Label(text="", size_hint=(1, 0.1))
        layout.add_widget(self.timer_label)

        return layout

    def start_quiz(self, instance):
        self.questions = []
        # Add 20 questions here
        self.questions.append(('MCQ', "What is 5 x 7?", ['30', '35', '40', '42'], 3))
        self.questions.append(('MCQ', "What is 8 x 9?", ['64', '72', '80', '88'], 1))
        self.questions.append(('MCQ', "What is 12 x 6?", ['62', '64', '68', '72'], 3))
        self.questions.append(('MCQ', "What is 9 x 4?", ['32', '36', '40', '44'], 1))
        self.questions.append(('MCQ', "What is 3 x 8?", ['20', '22', '24', '26'], 2))
        self.questions.append(('MCQ', "What is 7 x 5?", ['30', '32', '35', '36'], 0))
        self.questions.append(('MCQ', "What is 6 x 9?", ['48', '50', '54', '58'], 2))
        self.questions.append(('MCQ', "What is 11 x 3?", ['30', '33', '36', '39'], 1))
        self.questions.append(('MCQ', "What is 4 x 10?", ['36', '38', '40', '42'], 2))
        self.questions.append(('MCQ', "What is 2 x 12?", ['22', '24', '26', '28'], 1))
        self.questions.append(('MCQ', "What is 10 x 5?", ['40', '42', '44', '46'], 0))
        self.questions.append(('MCQ', "What is 8 x 7?", ['48', '50', '52', '56'], 3))
        self.questions.append(('MCQ', "What is 3 x 9?", ['24', '26', '28', '30'], 0))
        self.questions.append(('MCQ', "What is 6 x 8?", ['40', '42', '44', '48'], 3))
        self.questions.append(('MCQ', "What is 5 x 10?", ['45', '50', '55', '60'], 1))
        self.questions.append(('MCQ', "What is 11 x 4?", ['42', '44', '46', '48'], 0))
        self.questions.append(('MCQ', "What is 12 x 7?", ['72', '74', '76', '78'], 0))
        self.questions.append(('MCQ', "What is 9 x 3?", ['24', '27', '28', '30'], 3))
        self.questions.append(('MCQ', "What is 4 x 8?", ['28', '30', '32', '34'], 2))
        self.questions.append(('MCQ', "What is 7 x 6?", ['40', '42', '44', '46'], 1))

        # Add more MCQ and MSQ questions as needed

        random.shuffle(self.questions)
        self.correct_answers = 0
        self.total_questions = len(self.questions)
        self.start_button.disabled = True
        self.next_button.disabled = False
        self.submit_button.disabled = False
        self.show_next_question()

    def show_next_question(self):
        if len(self.questions) > 0:
            self.current_question = self.questions.pop(0)
            question_type, question_text, options, _ = self.current_question

            self.question_label.text = question_text
            self.answer_label.text = ""

            for button, option in zip(self.option_buttons, options):
                button.text = option
                button.state = 'normal'

            self.start_timer()
            self.next_button.disabled = True
            self.submit_button.disabled = False
        else:
            self.end_quiz()

    def select_option(self, instance):
        self.next_button.disabled = False

    def next_question(self, instance):
        self.check_answer()
        self.show_next_question()

    def check_answer(self, instance=None):
        if self.current_question[0] == 'MCQ':
            selected_option = None
            for index, button in enumerate(self.option_buttons):
                if button.state == 'down':
                    selected_option = index
                    break

            if selected_option == self.current_question[3]:
                self.correct_answers += 1
                self.answer_label.text = "Correct! Great job!"
                self.answer_label.color = (0, 1, 0, 1)
            else:
                self.answer_label.text = "Incorrect! Try again."
                self.answer_label.color = (1, 0, 0, 1)
        elif self.current_question[0] == 'MSQ':
            user_answers = []
            for index, button in enumerate(self.option_buttons):
                if button.state == 'down':
                    user_answers.append(index)

            if user_answers == self.current_question[3]:
                self.correct_answers += 1
                self.answer_label.text = "Correct! Great job!"
                self.answer_label.color = (0, 1, 0, 1)
            else:
                self.answer_label.text = "Incorrect! Try again."
                self.answer_label.color = (1, 0, 0, 1)

        self.next_button.disabled = False
        self.submit_button.disabled = True

    def start_timer(self):
        self.timer = 10
        Clock.schedule_interval(self.update_timer, 1)

    def update_timer(self, dt):
        self.timer -= 1
        self.timer_label.text = f"Time left: {self.timer} seconds"
        if self.timer <= 0:
            Clock.unschedule(self.update_timer)
            self.check_answer()

    def end_quiz(self):
        self.question_label.text = "Quiz complete!"
        self.answer_label.text = f"You answered {self.correct_answers} out of {self.total_questions} questions correctly."
        self.start_button.disabled = False
        self.next_button.disabled = True
        self.submit_button.disabled = True
        self.timer_label.text = ""

if __name__ == "__main__":
    MultiplicationQuiz().run()
