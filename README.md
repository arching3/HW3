# HW3 -- 마코프 연쇄 계산 코드

```C
#include <stdio.h>
#include <matrix.h>
//https://github.com/arching3/cmatrix


#define abs(a) (a<0?-a:a)
/*      Ak, Bk, Ck의 각 방문 확률
 *      ====================Ak====================
 *      Ak->Ak = 0.1 | Ak->Bk = 0.5 | Ak->Ck = 0.4
 *
 *      ====================Bk====================
 *      Bk->Ak = 0.6 | Bk->Ak = 0.1 | Bk->Ak = 0.3
 *
 *      ====================Ck====================
 *      Ck->Ak = 0.4 | Ck->Bk = 0.5 | Ck->Ck = 0.1
 * */


double sum(mat *);
// 행렬 내 모든 요소를 더하기 위한 함수


void swap(mat *,mat *);
// 두 인자를 바꾸는 함수


int main(){
        FILE *file_csv = fopen("markov_test.csv","w");
        fprintf(file_csv,"Ak,Bk,Ck,Diff_AVG,CNT\n");
	// 값들을 그리기 위해 파일에 저장하는 과정.



        double t[] = {0.333,0.333,0.333};
        double t1[] = {0.1,0.5,0.4,0.6,0.1,0.3,0.4,0.5,0.1};
	// 행렬 제작 위한 임시 배열

        mat *prob = mat_init_a(t,1,3);
        mat *prob_prev = mat_init(1,3);
        mat *weight = mat_init_a(t1,3,3);
        int cnt = 0;
	// 행렬 제작 및 연산 횟수를 세기 위한 변수 선언.


        for(;;cnt++){
	// if문에 의해 종료되므로 생략 가능.


                mat *temp = sub(prob,prob_prev);
                double total_diff = sum(temp)/3;
                mat_free(1,temp);
			// total_diff 정의. prob행렬과 prob_prev 행렬의 차를 구한 다음
			// 결과 행렬의 합을 구하고, 3으로 나눠 평균을 구해 total_diff를 정의한다.

                fprintf(file_csv,"%f,%f,%f,%f,%d\n",prob->arr[0],prob->arr[1],prob->arr[2],total_diff,cnt);
                printf("\n\n  |    Ak    |    Bk    |    Ck    | CNT = %d\n\n",cnt);
                print(prob);
			// 파일에 연산 값을 저장하고, 연산 과정을 콘솔에 출력.



                if(total_diff < 0.00001)break;
			// 수렴 종료 조건

                mat *temp1 = mat_copy(prob);
                swap(temp1,prob_prev);
                mat_free(1,temp1);
			// prob_prev의 값을 prob의 값으로 변경한다.

                mat *temp2 = mul(prob,weight);
                swap(temp2,prob);
                mat_free(1,temp2);
			// prob와 weight를 곱한다.


        }
        printf("\n\n\t      최종 결과\n         계산이 수행된 횟수 : %d\n\n  |    Ak    |    Bk    |    Ck    |\n",cnt);
        print(prob);
	//최종 결과를 출력한다.

        mat_free(3,prob,weight,prob_prev);
        fclose(file_csv);
        return 0;
}

double sum(mat *src){
        double rst = 0;
        for(int i = 0;i<src->r*src->c;i++){
                rst += abs(src->arr[i]);
        }
        return rst;
}

void swap(mat *a, mat *b){
        mat temp;
        temp = *b;
        *b = *a;
        *a = temp;
}
```
