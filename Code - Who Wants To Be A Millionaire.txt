# I have added the explanation to all of the functions created in the project in the first line of that class or function.

import tkinter as tk
from tkinter import messagebox, Text
import random

class Question:
    def __init__(self, statement, option1, option2, option3, option4, correct_option):
        self.statement = statement
        self.option1 = option1
        self.option2 = option2
        self.option3 = option3
        self.option4 = option4
        self.correct_option = correct_option

class WhoWantsToBeAMillionaire:
    # I am using this class to initialize the game with the main window, player details, and questions.
    def __init__(self, root):
        self.root = root
        self.root.title("Who Wants to Be a Millionaire")

        self.name = ""
        self.points = 0
        self.current_question = 1
        self.q = 0
        # Now, these question examples are from AI. I wanted to get them quickly and did not want to think a lot and find out different questions and their options.
        self.questions = [
            Question("Which Pakistani monument is considered one of the wonders of the world?",
                     "Badshahi Mosque", "Minar-e-Pakistan", "Faisal Mosque", "Shalimar Gardens", 2),
            Question("Which Pakistani cricketer holds the record for the most runs scored in Test cricket?",
                     "Wasim Akram", "Javed Miandad", "Inzamam-ul-Haq", "Younis Khan", 4),
            Question("Which Pakistani Nobel laureate is known for her work in education and girls' rights?",
                     "Malala Yousafzai", "Abdul Sattar Edhi", "Abdus Salam", "Dr. Atta-ur-Rahman", 1),
            Question("Which Pakistani city is known as the City of Gardens?",
                     "Lahore", "Karachi", "Islamabad", "Peshawar", 1),
            Question("Which Pakistani mountain range is home to the world's second-highest peak, K2?",
                     "Himalayas", "Karakoram", "Hindu Kush", "Pamir", 2),
            Question("Which Pakistani scientist is known as the \"Father of Pakistan's Nuclear Program\"?",
                     "Dr. Abdul Qadeer Khan", "Dr. Samar Mubarakmand", "Dr. Ishfaq Ahmad", "Dr. Pervez Hoodbhoy", 1),
            Question("Which Pakistani city is renowned for its centuries-old traditional pottery known as \"Multani Khussa\"?",
                     "Multan", "Karachi", "Faisalabad", "Sialkot", 1),
            Question("Which Pakistani poet and philosopher is known for his poetry collections \"Bang-e-Dra\" and \"Zarb-e-Kaleem\"?",
                     "Allama Iqbal", "Faiz Ahmed Faiz", "Habib Jalib", "Ahmed Faraz", 1),
            Question("Which Pakistani woman made history by becoming the first female Prime Minister of a Muslim-majority country?",
                     "Fatima Jinnah", "Benazir Bhutto", "Asma Jahangir", "Hina Rabbani Khar", 2)
        ]

        self.correct_answer_frequencies = {q.statement: 0 for q in self.questions}

        self.create_name_screen()

    def create_name_screen(self):
        # I am using this function/method to create the initial screen where the player enters their name.
        self.clear_screen()

        self.name_label = tk.Label(self.root, text="Enter your name:")
        self.name_label.pack(pady=10)

        self.name_field = tk.Entry(self.root)
        self.name_field.pack(pady=10)

        self.name_submit_button = tk.Button(self.root, text="Submit", command=self.submit_name)
        self.name_submit_button.pack(pady=10)

    def submit_name(self):
        # Now, the the submission of the player's name and moving to the question screen.
        self.name = self.name_field.get()
        if self.name:
            self.create_question_screen()
        else:
            messagebox.showwarning("Input Error", "Please enter your name!")

    def create_question_screen(self):
        # This is gonna help me create the screen displaying a quiz question with answer options.
        self.clear_screen()
        if self.current_question <= 3:
            self.q = self.random_question_thrower()
            question = self.questions[self.q]

            self.question_label = tk.Label(self.root, text=question.statement, wraplength=600)
            self.question_label.pack(pady=20)

            self.option_buttons = []
            options = [question.option1, question.option2, question.option3, question.option4]
            for i, option in enumerate(options, start=1):
                button = tk.Button(self.root, text=option, command=lambda i=i: self.answer_check(i))
                button.pack(pady=5)
                self.option_buttons.append(button)

            self.fifty_fifty_button = tk.Button(self.root, text="50/50", command=self.fifty_fifty)
            self.fifty_fifty_button.pack(side=tk.LEFT, padx=20, pady=20)

            self.expert_opinion_button = tk.Button(self.root, text="Expert's Opinion", command=self.expert_opinion)
            self.expert_opinion_button.pack(side=tk.RIGHT, padx=20, pady=20)
        else:
            self.display_winner()

    def random_question_thrower(self):
        # As clear from the title, select a random question index based on the current question number.
        return random.randint(0, 2) + ((self.current_question - 1) * 3)

    def answer_check(self, option_number):
        # This is specifically for checking if the selected answer is correct and proceed accordingly.
        if option_number == self.questions[self.q].correct_option:
            messagebox.showinfo("Correct", "Correcto! 50 points to Gryffindor! ... I mean to you.")
            self.points += 50
            self.current_question += 1
            self.correct_answer_frequencies[self.questions[self.q].statement] += 1
            self.create_question_screen()
        else:
            self.game_over()

    def game_over(self):
        # This handles the game over scenario and display the player's score.
        self.clear_screen()

        with open("Records.txt", "a") as f:
            f.write(f"{self.name}: {self.points}\n")

        self.individual_result = tk.Label(self.root, text=f"Aaah! Game over. You achieved {self.points} points.")
        self.individual_result.pack(pady=10)

        self.records = Text(self.root, height=10, width=50)
        self.records.pack(pady=10)

        try:
            with open("Records.txt", "r") as f:
                records_text = f.read()
                self.records.insert(tk.END, records_text)
        except FileNotFoundError:
            pass

        self.exit_button = tk.Button(self.root, text="Exit", command=self.root.quit)
        self.exit_button.pack(pady=10)

    def display_winner(self):
        # Display the winner screen and the player's final score.😃
        self.clear_screen()

        with open("Records.txt", "a") as f:
            f.write(f"{self.name}: {self.points}\n")

        self.win_label = tk.Label(self.root, text=f"You have won with {self.points} points")
        self.win_label.pack(pady=10)

        self.records = Text(self.root, height=10, width=50)
        self.records.pack(pady=10)

        try:
            with open("Records.txt", "r") as f:
                records_text = f.read()
                self.records.insert(tk.END, records_text)
        except FileNotFoundError:
            pass

        self.exit_button = tk.Button(self.root, text="Exit", command=self.root.quit)
        self.exit_button.pack(pady=10)

    def fifty_fifty(self):
        # This gets activated when we press the lifeline button. Nice name, no?😅
        correct_option = self.questions[self.q].correct_option
        incorrect_options = [i for i in range(1, 5) if i != correct_option]
        to_disable = random.sample(incorrect_options, 2)
        for i in to_disable:
            self.option_buttons[i-1].config(state=tk.DISABLED)
        self.fifty_fifty_button.config(state=tk.DISABLED)

    def expert_opinion(self):
        # Provide an expert's opinion as a hint for the current question.
        opinions = [
            "Najam mentioned once that even pigeons aspire to grow taller under the shadow of this monument.",
            "Najam joked about a cricketer who ran more on the pitch than he ever did in his dreams.",
            "Najam was heard whispering about a young lady who made big headlines and even bigger impacts on education.",
            "Najam once said, 'In which city can you get lost among flowers faster than you can find a parking spot?'",
            "Najam mumbled about a rocky range where the air is thin, but the views are breathtaking.",
            "Najam once mentioned a scientist who made atoms dance to his tune, and nations dance to his fear.",
            "Najam dropped a hint about a city where even the clay has its fashion statement.",
            "Najam chuckled once about a poet who made pens weep and hearts sing.",
            "Najam once joked about a lady who wore power suits and carried a country on her shoulders."
        ]
        self.opinion_label = tk.Label(self.root, text=opinions[self.q])
        self.opinion_label.pack(pady=10)
        self.expert_opinion_button.config(state=tk.DISABLED)

    def clear_screen(self):
        # Difficult to explain😁
        for widget in self.root.winfo_children():
            widget.destroy()

if __name__ == "__main__":
    root = tk.Tk()
    app = WhoWantsToBeAMillionaire(root)
    root.mainloop()
