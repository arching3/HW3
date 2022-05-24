# HW3 -- 마코프 연쇄 계산 코드

```C
#include <stdio.h>
#include <matrix.h>

#define abs(a) (a<0?-a:a)
/*      Ak, Bk, Ck의 각 방문 비율
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
void swap(mat *,mat *);

int main(){
        FILE *file_csv = fopen("markov_test.csv","w");
        fprintf(file_csv,"Ak,Bk,Ck,Diff_AVG,CNT\n");

        double t[] = {0.333,0.333,0.333};
        double t1[] = {0.1,0.5,0.4,0.6,0.1,0.3,0.4,0.5,0.1};

        mat *prob = mat_init_a(t,1,3);
        mat *prob_prev = mat_init(1,3);
        mat *weight = mat_init_a(t1,3,3);
        int cnt = 0;

        for(;;cnt++){
                mat *temp = sub(prob,prob_prev);
                double total_diff = sum(temp)/3;
                mat_free(1,temp);


                fprintf(file_csv,"%f,%f,%f,%f,%d\n",prob->arr[0],prob->arr[1],prob->arr[2],total_diff,cnt);
                printf("\n\n  |    Ak    |    Bk    |    Ck    | CNT = %d\n\n",cnt);
                print(prob);

                if(total_diff < 0.00001)break;

                mat *temp1 = mat_copy(prob);
                swap(temp1,prob_prev);
                mat_free(1,temp1);

                mat *temp2 = mul(prob,weight);
                swap(temp2,prob);
                mat_free(1,temp2);
        }
        printf("\n\n\t      최종 결과\n         계산이 수행된 횟수 : %d\n\n  |    Ak    |    Bk    |    Ck    |\n",cnt);
        print(prob);

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
