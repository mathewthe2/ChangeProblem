# ChangeProblem
Revisiting a simple change problem with C++

Problem: we have an amount n in a vendor machine that we need to convert into dollar and penny change. Let’s start with the brute-force solution in C++ by counting the bills from 10 to 0.1 and subtracting them one by one. In the main we would have a fixed array
 ```c++
 const double bills[7] = {10, 5, 2, 1, .5, .2, .1};
 ```
which we loop over each of the indices for the amount n to be disintegrated:
 ```c++
for (int k = 0; k < 7; k++){
    loop_down(bills[k], n);
}
```
In the loop_down function, we simply store the count of each type of bill in temp:
 ```c++
void loop_down(const double bill, double &n) {
    int temp(0);
    while (round(n*10) >= round(bill*10)) {
        n -= bill;
        temp += 1;
    } 
    if (temp > 0) {
        cout << bill << 'x' << temp << ' ';
        //sample output 25.8 = 10x2 5x1 0.5x1 0.2x1 0.1*1 
    }
}
```
Realistically, with types of bills k < 10 and the amount n < 1000 for a vendor machine, this solution is  good enough and easy to be modified. Nothing needs to be done under the general rule of ‘no pre-optimisation’ or ‘no optimisation unless required’. But what if we want to do so nevertheless? Try storing the quotient directly to temp instead of subtracting with a loop:
 ```c++
void loop_down(const double bill, double &n) {
    int temp = round(n*10)/round(bill*10); 
    n -= temp*bill;
    if (temp > 0) {
        cout << bill << 'x' << temp << ' ';
        //sample output 25.8 = 10x2 5x1 0.5x1 0.2x1 0.1*1 
    }
}
```
This reduces the computation for the amount of each type of bill. Still it seems that something could be done. We already know that 
any amount n >= 10 has to be reduced to a single digit value only by 10 dollar bills,
number of 5 dollar bills is always <= 1, 
4=2x2, 3=2x1+1x1, 2=2x1, 1=1x1, in other words, only 2s in evens, no 2s in 1.

There is only one single decimal place, so 10*n is an integer.

Pennies work similar to dollars but without the 10 dollar bill.

Using the above information, we can throw all the computation into the main function, leaving us with
 ```c++
    double amount;  
    cin >> amount; 
    int n = floor(amount*10);
    cout << amount << '=';
    if (n >= 100) {
        cout << "10x" << floor(n/100) << ' ';
        n -= 100*floor(n/100);
    }
    if (n >= 50) {
        cout << "5x1 ";
        n -= 50;
    }
    switch(int(floor(n/10))) {
        case (4):
            cout << "2x2 "; break;
        case (3):
            cout << "2x1 1x1 "; break;
        case (2):
            cout << "2x2 "; break;
        case (1):
            cout << "1x1 "; break;
    }
    n -= floor(n/10)*10; //this is pennies
    if (n >= 5) {
        cout << "0.5x1 ";
        n -= 5;
    }
    switch(n) {
        case (4):
            cout << "0.2x2 "; break;
        case (3):
            cout << "0.2x1 0.1x1"; break;
        case (2):
            cout << "0.2x2"; break;
        case (1):
            cout << "0.1x1"; break;
    }
```
We sacrificed flexibility, modifiability, and even readability of the code to reduce a fraction of the computation by known information.The previous solution always takes 7 turns of arithmetic calculation for any amount, while this solution takes <= 5 turns.  But also keep in mind of the resources involved in branching with conditional statements.
