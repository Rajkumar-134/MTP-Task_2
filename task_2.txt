def check_word(word, correct_words):
    if word in correct_words:
        return True, None  # Word is correct, no suggestions needed
    else:
        suggestions = difflib.get_close_matches(word, correct_words, n=3)
        return False, suggestions  # Word is incorrect, return suggestions
def suggest_words(event):
    global consecutive_wrong_words
    english_sentence = text_input.get("1.0", "end-1c").lower()

    if event.keysym=='space':
        # Check if the entered English sentence contains incorrect words
        incorrect_words = []
        words = english_sentence.split()
        word = words[-1] if words else ""
        is_correct, suggestions = check_word(word, eng_vocab)
        if not is_correct:
            incorrect_words.append((word, suggestions))
            if len(consecutive_wrong_words) >= 2:
 # Show error message with list of consecutive wrong words and suggestions
                error_message = "You have entered 2 consecutive wrong words:\n"
                for wrong_word, wrong_suggestions in consecutive_wrong_words:
                    error_message += f"{wrong_word}\n"
                    if wrong_suggestions:
                        error_message += "Suggestions:\n"
                        for suggestion in wrong_suggestions:
                            error_message += f"- {suggestion}\n"
                messagebox.showerror("Consecutive Wrong Words", error_message)
                    # Clear the list of consecutive wrong words after showing the error
                consecutive_wrong_words = []
                return  # Exit the function after showing the error message
                # Append wrong word and suggestions to consecutive wrong words list
            consecutive_wrong_words.append((word, suggestions))
        else:
            consecutive_wrong_words = []  # Reset the list if a correct word is entered
        if incorrect_words:
            error_message = "The following words are not available:\n"
            for word, suggestions in incorrect_words:
                error_message += f"{word}\n"
                if suggestions:
                    error_message += "Suggestions:\n"
                    for suggestion in suggestions:
                        error_message += f"- {suggestion}\n"
            # Show error message with suggestions in a messagebox
            messagebox.showerror("Word Suggestions", error_message)
            return  # Exit the function if there are incorrect words
def handle_translate():
    global consecutive_wrong_words
    selected_language = language_var.get()
    english_sentence = text_input.get("1.0", "end-1c")
    translation = None  # Initialize the translation variable
        # Translation logic here...
    try:
        if selected_language == "French":
            translation = translate_to_french(english_sentence)
        elif selected_language == "Spanish":
            translation = translate_to_spanish(english_sentence)
    except Exception as e:
        # Handle the translation error gracefully
        translation_output.delete("1.0", "end")
        translation_output.insert("end", f"Error during translation: {e}")

    # Update the translation output
    if translation is not None:
        translation_output.delete("1.0", "end")
        print(translation)
        translation_output.insert("end", f"{selected_language} translation: {translation}")
