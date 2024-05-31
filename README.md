  import pandas as pd
  import Levenshtein

  class SimpleChatBot:
      def __init__(self, filepath):
          # CSV 파일에서 데이터를 로드하고 질문과 답변을 리스트로 저장
          self.questions, self.answers = self.load_data(filepath)
      def load_data(self, filepath):
          # CSV 파일을 읽어 데이터를 pandas 데이터프레임으로 변환
          data = pd.read_csv(filepath)
          # 'Q' 열에서 질문을 리스트로 추출
          questions = data['Q'].tolist()
          # 'A' 열에서 답변을 리스트로 추출
          answers = data['A'].tolist()
          return questions, answers
      def find_best_answer(self, input_sentence):
          # 입력 문장과 학습 데이터의 각 질문 간의 레벤슈타인 거리를 계산하여 리스트로 저장
          distances = [Levenshtein.distance(input_sentence, question) for question in self.questions]
          # 가장 작은 거리의 인덱스를 찾음
          best_match_index = distances.index(min(distances))
          # 상위 5개의 유사한 질문과 유사도 계산 (거리가 작은 순서대로 정렬)
          top_5_indices = sorted(range(len(distances)), key=lambda i: distances[i])[:5]
          top_5_questions = [(self.questions[i], distances[i]) for i in top_5_indices]
          # 상위 5개의 유사한 질문과 거리를 출력
          print("Top 5 similar questions:")
          for i, (question, distance) in enumerate(top_5_questions):
              print(f"{i + 1}. Question: {question}, Distance: {distance}")
          # 가장 유사한 질문에 대한 답변을 반환
          return self.answers[best_match_index]
          # CSV 파일 경로를 지정하세요.
          filepath = 'ChatbotData.csv'
          # 챗봇 인스턴스를 생성
          chatbot = SimpleChatBot(filepath)
          
# '종료'라는 단어가 입력될 때까지 챗봇과의 대화를 반복
while True:
    input_sentence = input('You: ')
    if input_sentence.lower() == '종료':
        break
    response = chatbot.find_best_answer(input_sentence)
    print('Chatbot:', response)
