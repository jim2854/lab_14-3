# lab_14-3

#include <iostream>
#include <vector>
using namespace std;

typedef struct coo_sparse_matrix_struct{
    unsigned long rows;
    unsigned long cols;
    vector<unsigned long> row_ind;
    vector<unsigned long> col_ind;
    vector<long> val;
} coo_sparse_matrix;

typedef struct matrix_struct{
    unsigned long rows;
    unsigned long cols;
    vector<vector<long> > val;
} matrix;

void sort_col_ind(coo_sparse_matrix &coo_matrix){
    for(int i = 0; i < coo_matrix.col_ind.size(); i++)
    {
        for(int j = i + 1; j < coo_matrix.col_ind.size(); j++)
        {
            if(coo_matrix.col_ind[i] > coo_matrix.col_ind[j])
            {
                swap(coo_matrix.col_ind[i], coo_matrix.col_ind[j]);
                swap(coo_matrix.row_ind[i], coo_matrix.row_ind[j]);
                swap(coo_matrix.val[i], coo_matrix.val[j]);
            }
        }
    }
}

void sort_row_ind(coo_sparse_matrix &coo_matrix){
    for(int i = 0; i < coo_matrix.row_ind.size(); i++)
    {
        for(int j = i + 1; j < coo_matrix.row_ind.size(); j++)
        {
            if(coo_matrix.row_ind[i] > coo_matrix.row_ind[j]|| (coo_matrix.row_ind[i] == coo_matrix.row_ind[j] && coo_matrix.col_ind[i] > coo_matrix.col_ind[j]))
            {
                swap(coo_matrix.row_ind[i], coo_matrix.row_ind[j]);
                swap(coo_matrix.col_ind[i], coo_matrix.col_ind[j]);
                swap(coo_matrix.val[i], coo_matrix.val[j]);
            }
        }
    }
}

void sort_coo_matrix(coo_sparse_matrix &coo_matrix){
    sort_col_ind(coo_matrix);
    sort_row_ind(coo_matrix);
}

bool parse_coo_matrix(coo_sparse_matrix &coo_matrix){
    long num=0;
    string input;
    cin>>num;
    if(num<=0){
        return 1;
    }
    else{
        coo_matrix.rows=num;
    }
    cin>>num;
    if(num<=0){
        return 1;
    }
    else{
        coo_matrix.cols=num;
    }
    cin>>num;
    if(num<0){
        return 1;
    }

    cin.ignore();

    for(int k=0;k<num;k++){
        getline(cin,input);
        coo_matrix.row_ind.push_back(0);
        coo_matrix.col_ind.push_back(0);
        coo_matrix.val.push_back(0);
        for(int j=0;j<input.length();j++){
            int i=coo_matrix.row_ind.size()-1;
            while(input[j]!=' '){
                coo_matrix.row_ind[i]=coo_matrix.row_ind[i]*10+input[j]-'0';
                j++;
            }
            j++;
            while(input[j]!=' '){
                coo_matrix.col_ind[i]=coo_matrix.col_ind[i]*10+input[j]-'0';
                j++;
            }
            j++;
            while(input[j]!='\0'){
                coo_matrix.val[i]=coo_matrix.val[i]*10+input[j]-'0';
                j++;
            }
    
            if(coo_matrix.row_ind[i]>=coo_matrix.rows){
                return 1;
            }
            if(coo_matrix.col_ind[i]>=coo_matrix.cols){
                return 1;
            }
        }
    }

    for(int i=coo_matrix.row_ind.size()-1;i>=0;i--){
        if(i>=coo_matrix.row_ind.size()){
            continue;
        }
        for(int j=i-1;j>=0;j--){
            if(coo_matrix.row_ind[i]==coo_matrix.row_ind[j]&&coo_matrix.col_ind[i]==coo_matrix.col_ind[j]){
                coo_matrix.row_ind.erase(coo_matrix.row_ind.begin()+j);
                coo_matrix.col_ind.erase(coo_matrix.col_ind.begin()+j);
                coo_matrix.val.erase(coo_matrix.val.begin()+j);
            }
        }
    }

    for(int i=coo_matrix.val.size()-1 ;i>=0;i--){
        if(coo_matrix.val[i]==0){
            coo_matrix.row_ind.erase(coo_matrix.row_ind.begin()+i);
            coo_matrix.col_ind.erase(coo_matrix.col_ind.begin()+i);
            coo_matrix.val.erase(coo_matrix.val.begin()+i);
        }
    }
    
    return 0;
}

void print_coo_matrix(coo_sparse_matrix &coo_matrix){
    sort_coo_matrix(coo_matrix);
    cout<<coo_matrix.rows<<endl<<coo_matrix.cols<<endl;
    for(int i=0;i<coo_matrix.val.size();i++){
        cout<<coo_matrix.row_ind[i]<<" "<<coo_matrix.col_ind[i]<<" "<<coo_matrix.val[i]<<endl;
    }
}

matrix convert_coo_matrix_to_matrix(const coo_sparse_matrix &coo_matrix){
    matrix mat;
    mat.rows=coo_matrix.rows;
    mat.cols=coo_matrix.cols;
    for(int i=0;i<mat.rows;i++){
        mat.val.push_back(vector<long>());
        for(int j=0;j<mat.cols;j++){
            mat.val[i].push_back(0);
        }
    }

    for(int i=0;i<coo_matrix.row_ind.size();i++){
        mat.val[coo_matrix.row_ind[i]][coo_matrix.col_ind[i]]=coo_matrix.val[i];
    }
    return mat;
}

void print_matrix(const matrix &matrix){
    for(int i=0;i<matrix.rows;i++){
        for(int j=0;j<matrix.cols;j++){
            cout<<matrix.val[i][j]<<" ";
        }
        cout<<endl;
    }
    cout<<endl;
}

bool is_not_correct(const coo_sparse_matrix &coo_matrix_M,const coo_sparse_matrix &coo_matrix_N){
    if(coo_matrix_M.cols!=coo_matrix_N.rows){
        return 1;
    }
    else{
        return 0;
    }
}

coo_sparse_matrix mult_coo_matrix(const coo_sparse_matrix &coo_matrix_M,const coo_sparse_matrix &coo_matrix_N){
    coo_sparse_matrix solution;
    for(int i=0;i<coo_matrix_M.row_ind.size();i++){
        for(int j=0;j<coo_matrix_N.row_ind.size();j++){
            if(coo_matrix_M.col_ind[i]==coo_matrix_N.row_ind[j]){
                solution.row_ind.push_back(coo_matrix_M.row_ind[i]);
                solution.col_ind.push_back(coo_matrix_N.col_ind[j]);
                solution.val.push_back(coo_matrix_M.val[i]*coo_matrix_N.val[j]);
            }
        }
    }
    solution.rows=coo_matrix_M.rows;
    solution.cols=coo_matrix_N.cols;
    return solution;
}

int main(){
    coo_sparse_matrix coo_matrix_M,coo_matrix_N,coo_matrix_P;
    cout<<"Input COO matrix M:"<<endl;
    bool invalid=parse_coo_matrix(coo_matrix_M);
    if(invalid==1){
        cout<<"Invalid matrix."<<endl<<endl;
        return 0;
    }
    cout << "The COO matrix M is:" << endl;
    print_coo_matrix(coo_matrix_M);

    cout<<"Input COO matrix N:"<<endl;
    invalid=parse_coo_matrix(coo_matrix_N);
    if(invalid==1){
        cout<<"Invalid matrix."<<endl<<endl;
        return 0;
    }
    cout << "The COO matrix N is:" << endl;
    print_coo_matrix(coo_matrix_N);


    bool size=is_not_correct(coo_matrix_M,coo_matrix_N);
    if(size==1){
        cout<<"Invalid matrix."<<endl<<endl;
        return 0;
    }
    coo_matrix_P = mult_coo_matrix(coo_matrix_M, coo_matrix_N);
    cout << "The COO matrix P = M * N is:" <<endl;
    print_coo_matrix(coo_matrix_P);
    matrix matrix_P = convert_coo_matrix_to_matrix(coo_matrix_P);
    cout << "The matrix P is:" <<endl;
    print_matrix(matrix_P);
    return 0;
}
