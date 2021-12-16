# My first program
我的第一个项目
歌手大赛成绩管理系统
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <time.h>
#include <io.h>

int login();
int Program();
int list(FILE *fp);
int addWork();
int enter();
int inquire(FILE *fp);
int ranking(FILE *fP);

int main()
{ 
    char dirs[2][20] = {"MainFile", "ProgramList"};
    int index;
    for (index = 0; index < 2; index++)
    {
        if (access(dirs[index], 0))
        {
            mkdir(dirs[index]);
        }
    }   
    system("cls");
    login();
    Program();
}
int login()
{
    char admin1[30], password1[30], Radmin[20], Rpassword[20];
    char admin2[30], password2[30], ch;
    FILE* fp = fopen("MainFile/register.txt", "a+");
    if ((ch = fgetc(fp)) == EOF){
        rewind(fp);
        printf("\n============歌手大赛成绩管理系统============\n");
        printf("\n     ------请先创建一个管理员账号------\n\n");
        printf("\t账号:");
        scanf("%s", admin1);
        printf("\t密码:");
        scanf("%s", password1);
        fprintf(fp, "%s %s", admin1, password1);
        system("cls");
        printf("           >>>新的账号创建成功<<<");
    }
    rewind(fp);
    printf("\n============歌手大赛成绩管理系统============\n");
    printf("\n        ------管理员账号登录------\n");
    printf("       账号:");
    scanf("%s", admin2);
    printf("       密码:");
    scanf("%s", password2);
    fscanf(fp,"%s", Radmin);                     //从文件读取账号
    fscanf(fp,"%s", Rpassword);                 //从文件读取密码
    if(strcmp(admin2, Radmin) == 0 && strcmp(password2, Rpassword) == 0){    //判断账号密码是否正确
        fclose(fp);
        system("cls");
        printf("                   >>>登录成功<<<"); 
    }
    else{
        system("cls");                             //清屏
        printf("           >>>账号或密码错误<<<");
        fclose(fp);
        login();
    }
}
int Program()
{
    printf("\n============请选择比赛项目或新建比赛项目============\n\n");
    FILE* program = fopen("MainFile/program.txt", "a+");              //打开文件
    char pro[50], num[20], workid[10], workname[50], user[100], pl[30] = "ProgramList/", ch;
    int i = 0,ret,count = -1,Count,end = 1;
    printf("        -------比赛编号------比赛名称-------\n");
    rewind(program);
    if ((ch = fgetc(program)) == EOF){
        rewind(program);
        printf("\n\t  暂未查询到比赛项目,请输入add添加\n");
    }
    while(!feof(program)){    
        fgets(user, 100, program);
        count++;
    }
    Count = count;
    rewind(program);
    while(count--){                 //输出文件中的比赛项目
        fscanf(program, "%s %s", workid, workname);
        printf("\t\t%s\t%s\n", workid, workname);
    }
    printf("\n====================================================\n");
    printf("\n    >>>请输入比赛编号或输入add新建比赛项目:");
    scanf("%s", num);
    if (strcmp("add", num) == 0){                       //添加新项目
        addWork();
    }
    else{
        rewind(program);                             //使文件重新回流
        for ( i = 0; i < Count; i++){
            fscanf(program, "%s %s", workid, workname);
            if (strcmp(num, workid) == 0){ 
                strcat(pl, num);          //判断文件是否已经建立
                strcat(pl, ".txt");                
                FILE* fp = fopen(pl, "a+");           //打开文件
                system("cls");                       //清屏
                printf("        %s", workname);
                fclose(program);
                list(fp);
                end = 0;                           //项目操作函数
                break;
            }
        }
        if (end){
            system("cls");                          //清屏
            printf("\t\t  >>>您的输入有误<<<");
            Program();
        }                        
    }   
    system("cls");                          //清屏
    fclose(program);
}
int list(FILE *fp)
{
    rewind(fp);                               //文件指向开头
    int num;
    printf("\n=============比赛详情=============\n\n");    //界面
    printf("1)选手成绩录入\n");
    printf("2)选手成绩查询\n");
    printf("3)查看成绩排行\n");
    printf("4)返回\n");
    printf("0)退出\n\n");
    printf("输入对应序号进行操作:");
    scanf("%d", &num);
    if (num == 1){                             //根据输入序号进行对应操作
        system("cls");
        enter(fp);
    }
    else if (num == 2){
        inquire(fp);
    }
    else if (num == 3){
        ranking(fp);
    }
    else if (num == 4){
        system("cls");
        Program();
    }
    else if (num != 1 && num != 2 && num != 3 && num!=4 && num != 0){
        system("cls");
        printf("\t>>>您的输入有误<<<");
        list(fp);                                 //重新输入
    }
    else if (num == 0){                          //结束程序
        system("cls");
        fclose(fp);
        printf(" ==========\n");
        printf("  感谢使用!\n");
        printf(" ==========\n");
        system("pause");
    } 
}
int addWork()
{
    system("cls");
    FILE* program = fopen("MainFile/program.txt", "a+");
    int t;
    char workname[30];
    time_t now;                             //获取一个五位数
    time(&now);
    t = now % 100000;
    printf("\n============请输入比赛项目名称============\n\n\t   >>>");
    scanf("%s", workname);
    fprintf(program, "%d %s\n", t, workname);
    system("cls");
    printf("\t     %s添加成功",workname);
    fclose(program);
    Program();
}
int enter(FILE *fp)
{
    printf("\n============选手成绩录入============\n");
    printf("\n     请按照以下格式录入学生成绩\n");
    printf("  选手姓名 成绩个数 成绩1 成绩2...\n");
    printf("            输入off返回\n");
    char stu[20];
    int gread[10],i,num = 0,all = 0, min = 10000, max = 0;
    float average;
    scanf("%s", stu);
    if (strcmp(stu, "off") == 0){               //读入选手姓名
        system("cls");
        list(fp);
    }
    else{
        scanf("%d", &num);
        for ( i = 0; i < num; i++){                //读入成绩
            scanf("%d", &gread[i]);
            all += gread[i];
            if (gread[i] > max){
                max = gread[i];                         //成绩处理
            }
            if (gread[i] < min){
                min = gread[i];
            } 
        }   
        average = 1.0 * (all - max - min) / (num - 2);
        fprintf(fp, "%s %d %.2f\n", stu, all, average);         //记入成绩
        system("cls");                                      //清屏
        printf("         >>>%s已录入<<<", stu);
        enter(fp);
    }                                //重新来一遍
}
int inquire(FILE *fp)
{
    rewind(fp);                          //使文件回到开头
    char name[20],i,workname[20];
    int all;
    float average;
    system("cls");
    printf("\n============选手成绩查询============\n\n");
    printf("       请输入选手姓名进行查询\n");
    printf("             输入off返回\n");
    printf("\n\t    >>>");
    scanf("%s", name);                                //用户输入选手姓名
    if (strcmp(name, "off") == 0){
            system("cls");
            list(fp);                            //判断是否结束
    }
    else{
        struct stu{                                          //定义选手信息结构体
            char workname[30];                                  //为数据交换定义一个中间结构体
            int all;
            float average;
        }stu[100],stu1;
        int j,count[100],a,Tall,num = 0,rank;
        char ch[100];
        while (!feof(fp)){
            fgets(ch, 100,fp);                     //判断选手个数
            num++;
        }
        num--;
        rewind(fp);
        for ( i = 0; i < num; i++){
            fscanf(fp, "%s %d %f", stu[i].workname, &stu[i].all, &stu[i].average);  //调出选手信息
            count[i] = stu[i].average;  
        }
        for ( j = 0; j < num; j++){                          //对选手信息进行排序
            for ( i = 0; i < num; i++){
                if (count[i] < count[i + 1]){
                    a = count[i+1];
                    count[i+1] = count[i];
                    count[i] = a;
                    strcpy(stu1.workname, stu[i + 1].workname);             //姓名交换
                    strcpy(stu[i + 1].workname, stu[i].workname);           
                    strcpy(stu[i].workname, stu1.workname);
                    stu1.all = stu[i].all;                                 //总分交换
                    stu[i].all = stu[i + 1].all;
                    stu[i + 1].all = stu1.all;
                    stu1.average = stu[i].average;                         //平均分交换
                    stu[i].average = stu[i + 1].average;
                    stu[i + 1].average = stu1.average;
                }
            }    
        }
        rewind(fp);
        for ( i = 0; i < 20; i++){
            fscanf(fp, "%s %d %f", workname, &all, &average);          //进行查询
            if (strcmp(name, workname) == 0){
                system("cls");
                for ( i = 0; i < num; i++){
                    if (strcmp(workname, stu[i].workname) == 0){
                        rank = i + 1;
                    } 
                } 
                printf("\n====================查询结果====================\n");
                printf("\n    姓名\t总分\t  最终得分\t排名\n");
                printf("    %s\t%d\t  %  .2f\t %d\n", workname, all, average, rank);
                printf("\n================================================\n");
                printf("\n       ");
                system("pause");
                system("cls");
                inquire(fp);
                break;
            }    
        }
        if (strcmp(name, "off") != 0 && strcmp(name, workname) != 0){           //无对应姓名
                system("cls");
                printf("\n\n==========查无此人==========\n\n     ");
                system("pause");
                inquire(fp);
        }
    }   
}
int ranking(FILE *fp)
{
    rewind(fp);
    system("cls");
    printf("\n==========选手成绩排行==========\n\n");
    struct stu{                                          //定义选手信息结构体
        char workname[30];                                  //为数据交换定义一个中间结构体
        int all;
        float average;
    }stu[100],stu1;
    int i,j,count[100],a,Tall,num = 0;
    char ch[100];
    while (!feof(fp)){
        fgets(ch, 100,fp);                     //判断选手个数
        num++;
    }
    num--;
    rewind(fp);
    for ( i = 0; i < num; i++){
        fscanf(fp, "%s %d %f", stu[i].workname, &stu[i].all, &stu[i].average);  //调出选手信息
        count[i] = stu[i].average;  
    }
    for ( j = 0; j < num; j++){                          //对选手信息进行排序
        for ( i = 0; i < num; i++){
            if (count[i] < count[i + 1]){
                a = count[i+1];
                count[i+1] = count[i];
                count[i] = a;
                strcpy(stu1.workname, stu[i + 1].workname);             //姓名交换
                strcpy(stu[i + 1].workname, stu[i].workname);           
                strcpy(stu[i].workname, stu1.workname);
                stu1.all = stu[i].all;                                 //总分交换
                stu[i].all = stu[i + 1].all;
                stu[i + 1].all = stu1.all;
                stu1.average = stu[i].average;                         //平均分交换
                stu[i].average = stu[i + 1].average;
                stu[i + 1].average = stu1.average;
            }
        }    
    }
    printf(" 姓名\t  总分\t最终得分   排名\n");                            //输出排行
    for ( i = 0; i < num; i++){
        printf(" %s\t  %d\t %.2f      %d\n", stu[i].workname, stu[i].all, stu[i].average,i+1);
    }
    printf("\n================================");
    printf("\n\n      ");
    system("pause");
    system("cls");
    list(fp);
}
```
