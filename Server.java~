import edu.purdue.cs.cs180.channel.*;
import java.util.LinkedList;
import java.util.Scanner;

class Server{
  public TCPChannel requesterChannel;//probably should have a get method for the request and responder channels but made them public insead because im lazy
  public TCPChannel responderChannel;
  LinkedList<Integer> requestersID;//list of backlogged requesters
  LinkedList<Integer> respondersID;//list of backlogged responders
  LinkedList<String> requestersLocation;//list of places that the requesters are located at.  Corresponds to the requester ID list
  LinkedList<String> respondersTeam;//list of teams that the responders are on.  Corresponds to the responder ID list.
  
  public Server(int reqPort, int recPort) {//init of all the object variables for the server
    requesterChannel = new TCPChannel(reqPort);
    responderChannel = new TCPChannel(recPort);
    requestersID = new LinkedList<Integer>();
    respondersID = new LinkedList<Integer>();
    requestersLocation = new LinkedList<String>();
    respondersTeam = new LinkedList<String>();
    
    requesterChannel.setMessageListener(new MessageListener(){
      public void messageReceived(String message, int clientID) {//when a message is received by a requester then...
        String currentRequestersLocation = message.substring(7, message.length()); //removes the starting Request: from the recieved message string
        
        try{
          if(respondersID.size() == 0){//if there are responders on call then...
            requesterChannel.sendMessage("Searching:", clientID); //tells the requester that it needs to wait
            requestersID.add(clientID); //adds the requester ID to the backlog
            requestersLocation.add(currentRequestersLocation);//adds the requester location to the backlog
            
          }else{//if there are no responders then...
            requesterChannel.sendMessage("Assigned:" + respondersTeam.getFirst(), clientID);//sends a message to the requester telling them a responder has been assigned and what team that responder is on
            responderChannel.sendMessage("Assigned:" + currentRequestersLocation, respondersID.getFirst());//tells the longest waiting responder they have been assigned and the location of the requester they are to help
          }
        }catch(ChannelException e){
          e.printStackTrace();
        }
      }
    });
    
    responderChannel.setMessageListener(
                                   new MessageListener(){
      public void messageReceived(String message, int clientID) { //when a message is recieved form a responder then...
        String currentRespondersTeam = message.substring(8, message.length());//removes the stating Response: form the recieved message string 
        
        try{
          if(requestersID.size() == 0){//if there are no backlogged requesters then...
            responderChannel.sendMessage("Searching:", clientID);//tell the responder they need to wait
            respondersID.add(clientID);//log the responder ID
            respondersTeam.add(currentRespondersTeam);//log the responders team
          }else{//if there are backlogged requesters then...
            requesterChannel.sendMessage("Assigned:" + currentRespondersTeam, requestersID.getFirst());//tell the longest waiting requester a responder has been assigned and the team of that responder
            responderChannel.sendMessage("Assigned:" + requestersLocation.getFirst(), clientID);//tell the responder they have been assigned and the location of the requester they are picking up
          }
        }catch(ChannelException e){
          e.printStackTrace();
        }
      }
    });
  }
  
  public static void main(String[] args){
    Scanner s = new Scanner(System.in);
    Server server = new Server(8888,9999);
    while(true){
      if(s.nextLine().equals("exit")){
        try{
          server.responderChannel.close();
          server.requesterChannel.close();
        }catch(ChannelException e){
          e.printStackTrace();
        }
        break;
      }
    }
  }
}

