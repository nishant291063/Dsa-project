// ai_chatbot.c 
#include <stdio.h> 
#include <stdlib.h> 
#include <string.h> 
#include <ctype.h> 
#define ALPHABET_SIZE 26 
#define MAX_RESPONSE 256 
#define MAX_INPUT 100 
// ------------------- TRIE SECTION -------------------- 
typedef struct TrieNode { 
 struct TrieNode *children[ALPHABET_SIZE]; 
 char *response; 
} TrieNode; 
TrieNode* createTrieNode() { 
 TrieNode* node = (TrieNode*)malloc(sizeof(TrieNode)); 
 int i; 
 for (i = 0; i < ALPHABET_SIZE; i++) node->children[i] = NULL; 
 node->response = NULL; 
 return node; 
} 
void insertTrie(TrieNode *root, char *key, char *response) { 
 TrieNode *crawl = root; 
 while (*key) { 
 if (isalpha(*key)) { 
 int index = tolower(*key) - 'a'; 
 if (!crawl->children[index]) 
 crawl->children[index] = createTrieNode(); 
 crawl = crawl->children[index]; 
 } 
 key++; 
 } 
 crawl->response = strdup(response); 
} 
char* searchTrie(TrieNode *root, char *key) { 
 TrieNode *crawl = root; 
 while (*key) { 
 if (isalpha(*key)) { 
 int index = tolower(*key) - 'a'; 
 if (!crawl->children[index]) 
 return NULL; 
 crawl = crawl->children[index]; 
 } 
 key++; 
 } 
 return crawl->response; 
} 
// ------------------- QUEUE SECTION -------------------- 
typedef struct QueueNode { 
 char message[MAX_INPUT]; 
 struct QueueNode* next; 
} QueueNode; 
typedef struct { 
 QueueNode *front, *rear; 
} MessageQueue; 
MessageQueue* createQueue() { 
 MessageQueue q = (MessageQueue)malloc(sizeof(MessageQueue)); 
 q->front = q->rear = NULL; 
 return q; 
} 
void enqueue(MessageQueue *q, char *message) { 
 QueueNode temp = (QueueNode)malloc(sizeof(QueueNode)); 
 strcpy(temp->message, message); 
 temp->next = NULL; 
 if (!q->rear) { 
 q->front = q->rear = temp; 
 return; 
 } 
 q->rear->next = temp; 
 q->rear = temp; 
} 
char* dequeue(MessageQueue *q) { 
 if (!q->front) return NULL; 
 QueueNode *temp = q->front; 
 char *msg = strdup(temp->message); 
 q->front = q->front->next; 
 if (!q->front) q->rear = NULL; 
 free(temp); 
 return msg; 
} 
// ------------------- LINKED LIST (History) -------------------- 
typedef struct HistoryNode { 
 char message[MAX_INPUT]; 
 struct HistoryNode* next; 
} HistoryNode; 
void addHistory(HistoryNode **head, const char *message) { 
 HistoryNode newNode = (HistoryNode)malloc(sizeof(HistoryNode)); 
 strcpy(newNode->message, message); 
 newNode->next = *head; 
 *head = newNode; 
} 
void showHistory(HistoryNode *head) { 
 prinƞ ("\n--- Chat History ---\n"); 
 while (head) { 
 prinƞ ("%s\n", head->message); 
 head = head->next; 
 } 
} 
// ------------------- MAIN FUNCTION -------------------- 
int main() { 
 TrieNode *botRoot = createTrieNode(); 
 MessageQueue *msgQueue = createQueue(); 
 HistoryNode *history = NULL; 
 // Predefined chatbot responses 
 insertTrie(botRoot, "hello", "Hi there! How can I help you today?"); 
 insertTrie(botRoot, "issue", "Can you please describe the issue in detail?"); 
 insertTrie(botRoot, "refund", "Sure, I can help you with the refund process."); 
 insertTrie(botRoot, "bye", "Thank you for chaƫ ng with us. Goodbye!");
 char userInput[MAX_INPUT]; 
 prinƞ ("AI Chatbot for Customer Support\nType 'exit' to quit.\n\n"); 
 while (1) { 
 prinƞ ("You: ");
 fgets(userInput, MAX_INPUT, stdin); 
 userInput[strcspn(userInput, "\n")] = 0; 
 if (strcmp(userInput, "exit") == 0) break; 
 enqueue(msgQueue, userInput); 
 addHistory(&history, userInput); 
 char *reply = searchTrie(botRoot, userInput); 
 if (reply) { 
 prinƞ ("Bot: %s\n", reply); 
 addHistory(&history, reply); 
 } else { 
 prinƞ ("Bot: I'm sorry, I don't understand. Could you rephrase?\n"); 
 addHistory(&history, "I'm sorry, I don't understand. Could you rephrase?"); 
 } 
 } 
 showHistory(history); 
 return 0; 
}
