# cpu-algorithm
# 201901671 문성현
## 허프만 코드
#include <stdio.h>
#include <stdlib.h>
#define MAX_ELEMENT 200

typedef struct TreeNode {
	int weight;
	char ch;
	struct TreeNode* left, * right;
}TreeNode;

typedef struct {
	TreeNode* ptree;//TreeNod와 연결//
	char ch;//문자//
	int key;//문자 빈도수,=weight//
}element;
//element들이 모여서 트리가됨//

typedef struct {
	element heap[MAX_ELEMENT];
	int heap_size;//유효 원소개수 1부터시작시킴//
}HeapType;
//힙은 트리의 종류//

HeapType * create() {
	return (HeapType*)malloc(sizeof(HeapType));
}

void init(HeapType* h) {
	h->heap_size = 0;
}

void insert_heap(HeapType* h, element item) {
	int i;
	i = ++(h->heap_size);
	while ((i != 1) && (item.key < h->heap[i / 2].key)) {
		h->heap[i] = h->heap[i / 2];
		i /= 2;

	}
	h->heap[i] = item;

}
element delete_heap(HeapType* h) {
	int parent, child;
	element item, temp;
	item = h->heap[1];
	temp = h->heap[(h->heap_size)--];
	parent = 1;
	child = 2;
	while (child <= h->heap_size) {
		if((child<h->heap_size)&&(h->heap[child].key>h->heap[child+1].key))
			child++;
		if (temp.key < h->heap[child].key)break;
		h->heap[parent] = h->heap[child];
		parent = child;
		child *= 2;
	}
	h->heap[parent] = temp;
	return item;
}
//이진 트리 생성 함수//
TreeNode* make_tree(TreeNode* left, TreeNode* right) {
	TreeNode* node = (TreeNode*)malloc(sizeof(TreeNode));
	node->left = left;
	node->right = right;
	return node;
}
//이진 트리 제거 함수//
void destroy_tree(TreeNode* root)
{
	if (root == NULL) return;
	destroy_tree(root->left);
	destroy_tree(root->right);
	free(root);
}
int is_leaf(TreeNode* root) {
	//leaf가 끝자락, 자식노드가 있는지 없는지 판단하는 함수//
	return !(root->left) && !(root->right);
}
void print_array(int codes[], int n) {
	for (int i = 0; i < n; i++)
		printf("%d", codes[i]);
	printf("\n");
}

void print_codes(TreeNode* root, int codes[], int top) {
	//1을 저장하고 순환호출//
	if (root->left) {
		codes[top] = 1;
		print_codes(root->left, codes, top + 1);
	}
	//0을 저장하고 순환호출//
	if (root->right) {
		codes[top] = 0;
		print_codes(root->right, codes, top + 1);
	}
	if (is_leaf(root)) {
		printf("%c ", root->ch);
		print_array(codes, top);
	}
}

//허프만 코드 생성함수//
void huffman_tree(int freq[], char ch_list[], int n) {
	int i;
	TreeNode* node, * x;
	HeapType* heap;
	element e, e1, e2;
	int codes[100];
	int top = 0;

	heap = create();
	init(heap);
	for (i = 0; i < n; i++) {
		node = make_tree(NULL, NULL);
		e.ch = node->ch = ch_list[i];
		e.key = node->weight = freq[i];
		e.ptree = node;
		insert_heap(heap, e);
	}
	for (i = 1; i < n; i++) {
		//최솟값을 가지는 두개의 노드 삭제//
		e1 = delete_heap(heap);
		e2 = delete_heap(heap);
		//두개의 노드를 합친다!//
		x = make_tree(e1.ptree, e2.ptree);
		e.key = x->weight = e1.key + e2.key;
		e.ptree = x;
		printf("%d + %d-> %d\n", e1.key, e2.key, e.key);
		insert_heap(heap, e);
	}
	e = delete_heap(heap);
	print_codes(e.ptree, codes, top);
	destroy_tree(e.ptree);
	free(heap);



}


int main() {
	char ch_list[] = { 's','i','n','t','e' };
	int freq[] = { 4,6,8,12,15 };
	huffman_tree(freq, ch_list, 5);
	return 0;

}

## 연속행렬의 최소곱

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>

int matrix[501][2];//첫번째[]=몇번째 행렬인지,두번째[]=0이면 행 1이면 열나타냄//
int DP[501][501];//최소곱 나타내는 배열//
int mat(int n);
int min(int x, int y) {
	return (x < y) ? x : y;
}

int main() {
	int n, i;
	printf("행렬의 개수 : \n");
	scanf_s("%d", &n);//n:행렬의 개수//
	printf("각 행렬의 행 과 열 입력:\n ");
	for (i = 0; i < n; i++) {
		scanf_s("%d %d", &matrix[i][0], &matrix[i][1]);//각 행렬의 행과 열 값 입력//
	}
	int result = mat(n);
	printf("최소연산 횟수 : %d", result);

}
int mat(int n) {
	int a, b;
	int i, j, k;
	for (i = 0; i < n; i++) {
		for (j = 0; j < n - i; j++) {
			a = j;
			b = j + i;
			if (a == b) {
				DP[a][b] = 0;
			}
			else {
				DP[a][b] = 1000000;
				for (k = a; k < b; k++) {
					DP[a][b] = min(DP[a][b], DP[a][k] + DP[k + 1][b] + (matrix[a][0] * matrix[k][1] * matrix[b][1]));
				}
			}
		}
	}
	return DP[0][n - 1];
}
//mat 코드 설명 : 이중포문에서 a=j,b=j+i의 경우를 0부터 손으로 쓰면서 해보았다.
//i=0 j=0 일때는 모든값이 a==b이므로 0
//나머지부터 else문 적용 DP=1000000으로 둔것은 최소값을 저장해야하기때문에 처음엔 엄청 큰수로 무조건 대입할 수 있게 지정한것
//DP[a][b]에서 k=a부터b까지 포문 돌리면 DP[a][b]의 최소곱 경우를 구할 수 있다.
//이렇게 구해진 최소곱들은 DP[][]배열에 저장되있고
//최종 구하는 값인 DP[0][n-1]값은 위 배열에 저장되어있는 값들을 통해 다이나믹 프로그래밍 구조로 계산이 이루어진다.
//그리고 return DP[0][n-1]해주면 입력한 행렬 의 최소곱을 얻을 수 있다