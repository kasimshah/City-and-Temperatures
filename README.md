# City-and-Temperatures


#include <cstdlib>
#include <iostream>
#include <string>
#include <sstream>
#include <ctype.h>
#include <string.h>
#include <fstream>
#include <iomanip>
#include <bitset>
#include <regex>
#include <vector>
#include<stack>
#include <cstring>

#define DUMMY_TRAILER "zzzzz"

using namespace std;

//Sturture for node of double linked list
struct node
{
    string state; //Store state
    string city; //Store City
    double temp; //Store temperature
    node *forw; //Point to next node
    node *back; //Point to previous node
};

//List class for performing actions on double lonked list
class List
{
private:
    //LISTNODE *list;
    
public:
    
    //Function to insert a node in the double linked list
    void insert(node *list, string state, string city, double temp)
    {
        
        node *curr = list->forw; //Set current pointer to the next value in the list
        node *prev = list; //Set current pointer to the top of the list
        node *cityComp;
        node *pnew; //Pointer for new node to be added to the list
        double sum=0.0;
        
        //while (name>curr->data)
        //While loop to find the correct place to insert the new node
        while(state.compare(curr->state)>0)
        {
            prev = curr;
            curr = curr->forw;
        }
        
        //if the state to insert in has the same name as another node first check if the city names are the same if not then add the state to the linked list
        if(state.compare(curr->state)==0)
        {
            
            cityComp=curr;
            
            //While loop to see if the state needed to be added has the same city name as the other nodes that have the same state name
            while((city.compare(cityComp->city)!=0) && (state.compare(cityComp->state)==0) )
            {
                
                cityComp=cityComp->forw;
                
            }
            
            //if the same city name is found for the same state name then average the two temperatures
            if((city.compare(cityComp->city)==0) && (state.compare(cityComp->state)==0) )
            {
                
                sum=cityComp->temp + temp;
                sum=sum/2.0;
                cityComp->temp=sum;
                
            }
            
            //If the state name is the same but the city name is different then create and add the new node to the double linked list
            else
            {
                pnew= new node;
                
                //Adding in the new node with the name
                (pnew->state).assign(state);
                (pnew->city).assign(city);
                pnew->temp = temp;
                pnew->forw = curr;
                pnew->back = prev;
                prev->forw = pnew;
                curr->back = pnew;
            }
            
            return;
            
        }
        
        //if the state name is different then create a new node and add it to the double linked list
        else if(state.compare(curr->state)!=0)
        {
            
            pnew= new node;
            
            //Adding in the new node with the name
            (pnew->state).assign(state);
            (pnew->city).assign(city);
            pnew->temp = temp;
            pnew->forw = curr;
            pnew->back = prev;
            prev->forw = pnew;
            curr->back = pnew;
            
            return;
        }
        
    }
    
    //Function call to go through the double linked list created and print out the contents in the list
    void traverse_forw(node *list)
    {
        
        list = list->forw; // skip the dummy node
        
        while (list->state != DUMMY_TRAILER)
        {
            
            cout<<list->city<<","<<list->state<<": "<<list->temp<<endl;
            
            list = list->forw;
            
        }
        
        return;
        
    }
    
    //Function call to go through the list and find the city in a certain state with the highest temperature
    void highestCity(node *list)
    {
        
        node *stateCurr;
        double highTemp;
        string currState;
        string highCity;
        
        list = list->forw; // skip the dummy node
        
        //While loop to go through the contents of the list
        while (list->state != DUMMY_TRAILER)
        {
            
            stateCurr=list;
            currState = list->state;
            highTemp = list->temp;
            highCity = list->city;
            
            //While loop to go through the cities within the same state and find the city with the highest temperature
            while(currState == stateCurr->state)
            {
                
                if( (currState == stateCurr->forw->state) && (highTemp < stateCurr->forw->temp) )
                {
                    
                    highTemp=stateCurr->forw->temp;
                    highCity = stateCurr->forw->city;
                    
                }
                
                stateCurr=stateCurr->forw;
                
            }
            
            cout<<"In "<<currState<<" the city with the highest temperature is "<<highCity<<" with average temperature of "<<highTemp<<endl;
            
            list = stateCurr;
            
        }
        
        return;
        
    }
    
};

void readFile(node *list);
node *init_list(void);

int main()
{
    
    List call; //Variable used to call the list class when needed
    node *list;
    string name;
    
    list=init_list(); //Initialize the double linked list
    
    readFile(list); //Function call to read in from the text file
    
    call.traverse_forw(list); //Call to print out the contents in the double linked list
    
    cout<<" "<<endl;
    
    call.highestCity(list); //Call to find the city with the highest temperature and print it out
    
    return 0; //End of program
    
}

//Function to initialize the list
node *init_list(void)
{
    node *list;
    
    list=new node;
    
    (list->state).assign(DUMMY_TRAILER);
    (list->city).assign(DUMMY_TRAILER);
    list->temp=0.00;
    list->forw = list;
    list->back = list;
    
    return list;
}

//Function to read through the text file and add the contents to the list
void readFile(node *list)
{
    
    ifstream file;
    string line; //Variable used to store contents of each line into a string
    List put; //variable to use class and out the names and temperaturs into the list
    //variable used to store the string into a char*
    char *ptState; //Variable used to hold the state name
    char *ptCity; //Variable used to hold the city name
    char *ptTemp; //Variable used to hold the temperature
    char *end;
    string state;
    string city;
    string name;
    double temp;
    file.open("/Users/kasimshah/Desktop/temperature.txt");
    
    while(file.good())
    {
        
        getline(file,line);
        
        char *chline= new char [line.length()+1];
        
        strcpy(chline,line.c_str());
        
        ptState = strtok(chline,",;:\t");
        
        ptCity = strtok(NULL,",;:\t");
        
        ptTemp = strtok(NULL,",;:\t");
        
        temp=strtod(ptTemp,&end);
        
        state=ptState;
        
        city=ptCity;
        
        put.insert(list,state,city,temp);
        
        delete[] chline;
        
    }
    
    file.close();
    
}


