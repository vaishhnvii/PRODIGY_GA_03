# PRODIGY_GA_03
Implement a simple text generation algorithm using Markov chains. This task involves creating a statistical model that predicts the probability of a character or word based on the previous one
# markov_text_generation.py

import random
from collections import defaultdict

class MarkovChain:
    def __init__(self, order=1):
        self.order = order
        self.start_tokens = []
        self.transition_probs = defaultdict(lambda: defaultdict(float))
    
    def train(self, text):
        tokens = text.split()
        self.start_tokens.append(tuple(tokens[:self.order]))
        
        for i in range(len(tokens) - self.order):
            current_state = tuple(tokens[i:i+self.order])
            next_state = tokens[i+self.order]
            self.transition_probs[current_state][next_state] += 1.0
        
        # Normalize probabilities
        for state, next_states in self.transition_probs.items():
            total_count = sum(next_states.values())
            for next_state in next_states:
                next_states[next_state] /= total_count
    
    def generate_text(self, max_length=100):
        current_state = random.choice(self.start_tokens)
        generated_text = list(current_state)
        
        while len(generated_text) < max_length:
            if current_state not in self.transition_probs:
                break
            next_state_probs = self.transition_probs[current_state]
            next_state = random.choices(list(next_state_probs.keys()), list(next_state_probs.values()))[0]
            generated_text.append(next_state)
            current_state = tuple(generated_text[-self.order:])
        
        return ' '.join(generated_text)

if __name__ == "__main__":
    # Example usage:
    text_corpus = """
    Markov chains, named after Andrey Markov, are mathematical systems that hop from one state to another, as a game character might move from one square to another in a board game. A Markov chain is a random process that undergoes transitions from one state to another on a state space. It is named after the Russian mathematician Andrey Markov.
    """
    
    # Initialize and train the Markov chain
    markov_model = MarkovChain(order=2)
    markov_model.train(text_corpus)
    
    # Generate text
    generated_text = markov_model.generate_text(max_length=50)
    print("Generated Text:")
    print(generated_text)

