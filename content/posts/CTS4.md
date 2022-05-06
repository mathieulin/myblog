---
title: "CTS4"
date: 2022-05-06T22:59:31+08:00
draft: false
author:Mathieu
---

## 这是CTS代码

```java
import java.io.*;
import java.util.*;

import static java.lang.Math.abs;


public class Test {

    static boolean isLogin = false;
    public static void main(String args[]) throws IOException {

        Scanner in = new Scanner(System.in);
        String argStr;
        Vector<User> userList = new Vector<User>();
        Vector<Line> lineList = new Vector<Line>();
        Vector<Train> trainList = new Vector<Train>();
        Vector stationList = new Vector();
        HashMap<String, Boolean> certMsg = new HashMap<String, Boolean>();
        User user = new User();
        boolean isAdmin = false;
        while (true) {
            argStr = in.nextLine();
            String[] splited = argStr.split("\\s+");
            if(Objects.equals(splited[0], "addUser")){
                addUser(userList, splited);
            }
            else if (Objects.equals(splited[0], "QUIT")){
                System.out.println("----- Good Bye! -----");
                System.exit(0);
            }
            else if(Objects.equals(splited[0], "login")){
                int index = login(splited, isLogin, userList);
                if( index != -1){
                    isLogin = true;
                    user = userList.get(index);
                }

            }
            else if(Objects.equals(splited[0], "logout")){
                if(logout(splited, isLogin)){
                    isLogin = false;
                    user = null;
                }
            }

            else if (Objects.equals(splited[0], "TunakTunakTun")){
                if(!isAdmin){
                    isAdmin = true;
                    System.out.println("DuluDulu");
                }
                else {
                    System.out.println("WanNiBa");
                }
            }
            else if (Objects.equals(splited[0], "NutKanutKanut")){
                if(isAdmin){
                    isAdmin = false;
                    System.out.println("DaDaDa");
                }
                else {
                    System.out.println("WanNiBa");
                }
            }
            else{
                if(Objects.equals(splited[0], "addLine")){
                    if(isAdmin){
                        addLine(lineList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "delLine")){
                    if(isAdmin){
                        delLine(lineList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "addStation")){
                    if(isAdmin){
                        addStation(lineList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "delStation")){
                    if(isAdmin){
                        delStation(lineList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "lineInfo")){
                    lineInfo(lineList, splited);
                }
                else if(Objects.equals(splited[0], "listLine")){
                    if(splited.length == 1){
                        listLine(lineList);
                    }
                    else{
                        System.out.println("Arguments illegal");
                        continue;
                    }
                }
                else if(Objects.equals(splited[0], "addTrain")){
                    if(isAdmin){
                        addTrain(lineList, trainList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "delTrain")){
                    if(isAdmin){
                        delTrain(lineList, trainList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "checkTicket")){
                    if(user.getAdmin() == false) {
                        checkTicket(lineList, trainList, splited);
                    }
                    else{
                        System.out.println("Command does not exist");
                    }
                }
                else if(Objects.equals(splited[0], "listTrain")){
                    listTrain(lineList, trainList, splited);
                }
                else if(Objects.equals(splited[0], "buyTicket")){
                    buyTicket(user, lineList, trainList, splited, isLogin);
                }
                else if(Objects.equals(splited[0], "listOrder")){
                    if(splited.length == 1){
                        listOrder(user, isLogin);
                    }
                    else{
                        System.out.println("Arguments illegal");
                        continue;
                    }
                }
                else if(Objects.equals(splited[0], "rechargeBalance")){
                    if(splited.length == 2){
                        rechargeBalance(user, isLogin, Double.parseDouble(splited[1]));
                    }
                    else{
                        System.out.println("Arguments illegal");
                        continue;
                    }
                }
                else if(Objects.equals(splited[0], "checkBalance")){
                    if(splited.length == 1){
                        checkBalance(user, isLogin);
                    }
                    else{
                        System.out.println("Arguments illegal");
                        continue;
                    }
                }
                else if(Objects.equals(splited[0], "cancelOrder")){
                    cancelOrder(user, lineList, trainList, splited, isLogin);
                }
                else if(Objects.equals(splited[0], "payOrder")){
                    payOrder(user, splited, isLogin);
                }
                else if(Objects.equals(splited[0], "importCert")){
                    if(isAdmin){
                        certMsg = importCert(certMsg, userList, splited);
                    }
                    else {
                        System.out.println("Command does not exist");
                    }
                }
                else {
                    System.out.println("Command does not exist");
                }
            }
        }
    }

    private static HashMap<String, Boolean> importCert(HashMap<String, Boolean> certMsg, Vector<User> userList, String[] args) throws IOException {
        if(args.length != 2) {
            System.out.println("Arguments illegal");
            return certMsg;
        }
        try{
            certMsg = Cert.read(certMsg, args[1], userList);
        }
        catch (IOException e){
            throw new RuntimeException();
        }

        return certMsg;
    }

    private static void payOrder(User user, String[] args, boolean isLogin) {
        if(args.length != 1) {
            System.out.println("Arguments illegal");
            return;
        }
        if(isLogin == false){
            System.out.println("Please login first");
            return;
        }
        Vector<Order> orders = user.getOrders();
        if(orders.size() == 0) {
            System.out.println("No order");
        }

        int discountNum;
        if(isStudent(user)){
            Student student = (Student)user;
            discountNum = student.getDiscountNum();
        }

        else {
            discountNum = 0 ;
        }
        double sum = 0;
        int orders_size = orders.size()-1;
        for(int i=orders_size;i>=0;i--){
            Order order = orders.get(i);
            if(order.isPaid()){
                continue;
            }
            if(discountNum >= order.getSeatNum()){
                sum += order.getSum()*0.05;
                discountNum -= order.getSeatNum();
            }
            else{
                double single = order.getSingle();

                sum += single*discountNum*0.05 + (order.getSeatNum()-discountNum)*single;
                discountNum = 0;
            }
        }
        if(sum<=user.getBalance()){
            user.setOrders(orders);
            double balance = user.getBalance()-sum;
            user.setBalance(balance);
            for(int i=orders_size;i>=0;i--){
                orders.get(i).setPaid(true);
            }
            System.out.println("Payment success");
            return;
        }
        else{
            System.out.println("Balance does not enough");
            return;
        }
    }

    private static boolean isStudent(User user){
        try {
            Student student = (Student) user;
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    private static void cancelOrder(User user, Vector<Line> lineList, Vector<Train> trainList, String[] args, boolean isLogin) {
        if(args.length <= 5) {
            System.out.println("Arguments illegal");
            return;
        }
        if(isLogin == false){
            System.out.println("Please login first");
            return;
        }

        String trainNum = args[1];
        int trainIndex = trainExist(trainList, trainNum);
        if( trainIndex == -1){
            System.out.println("Train does not exist");
            return;
        }

        Train train = trainList.get(trainIndex);
        Vector<Integer> ticket = train.getTicketNum();

        Vector<Order> orders = user.getOrders();
        int num = Integer.parseInt(args[5]);
        int sum = 0;
        int orders_size = orders.size()-1;
        for(int i=orders_size;i>=0;i--){
            Order order = orders.get(i);
            if(order.isPaid()){
                continue;
            }
            if(Objects.equals(args[1], order.getTrainNum())&&Objects.equals(args[2], order.getBegin())&&Objects.equals(args[3], order.getDestination())&&Objects.equals(args[4], order.getSeat())){
                sum++;
                if(num<order.getSeatNum()){
                    int seatNum = order.getSeatNum() - num;
                    order.setSeatNum(seatNum);
                    order.setSum(seatNum * order.getSingle());
                    user.setOrders(orders);
                    System.out.println("Cancel success");
                    if(Objects.equals(args[4], "CC") || Objects.equals(args[4], "SC") || Objects.equals(args[4], "1A")){
                        ticket.set(0, ticket.get(0) + num);
                        train.setTicketNum(ticket);
                    }
                    else if(Objects.equals(args[4], "HC") || (Objects.equals(args[4], "SB") && trainNum.charAt(0) == '0')|| Objects.equals(args[4], "2A")){
                        ticket.set(1, ticket.get(1) + num);
                        train.setTicketNum(ticket);
                    }
                    else if(Objects.equals(args[4], "GG") || (Objects.equals(args[4], "SB") && trainNum.charAt(0) == 'G')){
                        ticket.set(2, ticket.get(2) + num);
                        train.setTicketNum(ticket);
                    }
                    return;
                }
                else if(num == order.getSeatNum()){
                    orders.remove(i);
                    user.setOrders(orders);
                    System.out.println("Cancel success");
                    if(Objects.equals(args[4], "CC") || Objects.equals(args[4], "SC") || Objects.equals(args[4], "1A")){
                        ticket.set(0, ticket.get(0) + num);
                        train.setTicketNum(ticket);
                    }
                    else if(Objects.equals(args[4], "HC") || (Objects.equals(args[4], "SB") && trainNum.charAt(0) == '0')|| Objects.equals(args[4], "2A")){
                        ticket.set(1, ticket.get(1) + num);
                        train.setTicketNum(ticket);
                    }
                    else if(Objects.equals(args[4], "GG") || (Objects.equals(args[4], "SB") && trainNum.charAt(0) == 'G')){
                        ticket.set(2, ticket.get(2) + num);
                        train.setTicketNum(ticket);
                    }
                    return;
                }
                else{
                    num -= order.getSeatNum();
                    if(Objects.equals(args[4], "CC") || Objects.equals(args[4], "SC") || Objects.equals(args[4], "1A")){
                        ticket.set(0, ticket.get(0) + order.getSeatNum());
                        train.setTicketNum(ticket);
                    }
                    else if(Objects.equals(args[4], "HC") || (Objects.equals(args[4], "SB") && trainNum.charAt(0) == '0')|| Objects.equals(args[4], "2A")){
                        ticket.set(1, ticket.get(1) + order.getSeatNum());
                        train.setTicketNum(ticket);
                    }
                    else if(Objects.equals(args[4], "GG") || (Objects.equals(args[4], "SB") && trainNum.charAt(0) == 'G')){
                        ticket.set(2, ticket.get(2) + order.getSeatNum());
                        train.setTicketNum(ticket);
                    }

                    orders.remove(i);
                }
            }
        }
        if(sum == 0){
            System.out.println("No such Record");
            return;
        }
        else{
            System.out.println("No enough orders");
            return;
        }
    }

    private static void checkBalance(User user, boolean isLogin) {
        if (!isLogin) {
            System.out.println("Please login first");
            return;
        }
        System.out.format("UserName:%s\nBalance:%.2f\n", user.getName(), user.getBalance());
    }

    private static void rechargeBalance(User user, boolean isLogin, double num) {
        if(!isLogin){
            System.out.println("Please login first");
            return;
        }
        if(num<0){
            System.out.println("Arguments illegal");
            return;
        }

        double sum = num + user.getBalance();
        user.setBalance(sum);
        System.out.println("Recharge Success");
        return;

    }

    private static void listOrder(User user, boolean isLogin) {
        if(!isLogin){
            System.out.println("Please login first");
            return;
        }
        Vector<Order> orders = user.getOrders();
        int i;
        if(orders.isEmpty()){
            System.out.println("No order");
            return;
        }

        for(i=orders.size()-1;i>=0;i--){
            Order order = orders.get(i);
            System.out.format("[%s: %s->%s] seat:%s num:%d price:%.2f paid:%c\n",order.getTrainNum(),order.getBegin(),order.getDestination(),order.getSeat(),order.getSeatNum(),order.getSum(),(order.isPaid()?'T':'F'));
        }
    }

    private static void buyTicket(User user, Vector<Line> lineList, Vector<Train> trainList, String[] args, boolean isLogin) {
        if(args.length <= 5) {
            System.out.println("Arguments illegal");
            return;
        }
        if(isLogin == false){
            System.out.println("Please login first");
            return;
        }
        String trainNum = args[1];
        int trainIndex = trainExist(trainList, trainNum);
        if( trainIndex == -1){
            System.out.println("Train does not exist");
            return;
        }

        Train train = trainList.get(trainIndex);
        String lineName= train.getLine();
        int lineIndex = findLine(lineList, lineName);
        Line line = lineList.get(lineIndex);
        int beginIndex = stationExist(line, args[2]);
        int destIndex = stationExist(line, args[3]);
        if(beginIndex == -1 || destIndex == -1){
            System.out.println("Station does not exist");
            return;
        }
        Vector<Integer> mileage = line.getStationMileage();
        int distance = abs(mileage.get(destIndex) - mileage.get(beginIndex));

        Vector<Integer> ticket = train.getTicketNum();
        Vector<Double> price = train.getTicketPrice();

        int rest = 0;
        double prices = 0;
        int num = 100000;
        if(isInt(args[5])) {
            num = Integer.valueOf(args[5]);
        }

        if(trainNum.charAt(0) == '0'){
            if(Objects.equals(args[4], "CC")){
                rest = ticket.get(0);
                prices = price.get(0);
                if(rest>num){
                    ticket.set(0, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else if(Objects.equals(args[4], "SB")){
                rest = ticket.get(1);
                prices = price.get(1);
                if(rest>num){
                    ticket.set(1, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else if(Objects.equals(args[4], "GG")){
                rest = ticket.get(2);
                prices = price.get(2);
                if(rest>num){
                    ticket.set(2, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else{
                System.out.println("Seat does not match");
                return;
            }
        }
        else if(trainNum.charAt(0) == 'G'){
            if(Objects.equals(args[4], "SC")){
                rest = ticket.get(0);
                prices = price.get(0);
                if(rest>num){
                    ticket.set(0, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else if(Objects.equals(args[4], "HC")){
                rest = ticket.get(1);
                prices = price.get(1);
                if(rest>num){
                    ticket.set(1, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else if(Objects.equals(args[4], "SB")){
                rest = ticket.get(2);
                prices = price.get(2);
                if(rest>num){
                    ticket.set(2, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else{
                System.out.println("Seat does not match");
                return;
            }
        }
        else if(trainNum.charAt(0) == 'K'){
            if(Objects.equals(args[4], "1A")){

                if (user.isCert()){
                    System.out.println("Cert illegal");
                    return;
                }
                rest = ticket.get(0);
                prices = price.get(0);
                if(rest>num){
                    ticket.set(0, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else if(Objects.equals(args[4], "2A")){
                if (user.isCert()){
                    System.out.println("Cert illegal");
                    return;
                }
                rest = ticket.get(1);
                prices = price.get(1);
                if(rest>num){
                    ticket.set(1, rest - num);
                    train.setTicketNum(ticket);
                }
            }
            else{
                System.out.println("Seat does not match");
                return;
            }
        }

        if(!isInt(args[5]) || Integer.valueOf(args[5])<=0){
            System.out.println("Ticket number illegal");
            return;
        }
        else{

            if(num > rest){
                System.out.println("Ticket does not enough");
                return;
            }
        }
        Vector<Order> orders= user.getOrders();
        Order order = new Order();
        order.setTrainNum(args[1]);
        order.setBegin(args[2]);
        order.setDestination(args[3]);
        order.setSeat(args[4]);
        order.setSeatNum(num);
        order.setSingle(prices*distance);
        order.setSum(num*prices*distance);
        orders.addElement(order);
        user.setOrders(orders);
        System.out.println("Thanks for your order");
        return;
    }

    private static boolean logout(String[] args, boolean isLogin){
        if(args.length!=1){
            System.out.println("Arguments illegal");
            return false;
        }
        if(isLogin) {
            System.out.println("Logout success");
            return true;
        }
        else{
            System.out.println("No user has logged in");
            return false;
        }
    }

    private static int login(String[] args, boolean isLogin,  Vector<User> userList){
        if(args.length!=3){
            System.out.println("Arguments illegal");
            return -1;
        }
        else if(isLogin == true){
            System.out.println("You have logged in");
            return -1;
        }
        String card = args[1];
        String name = args[2];
        boolean isExist = false;
        int i;
        for(i=0;i< userList.size();i++){
            if(Objects.equals(userList.get(i).getAadhaar(), card)){
                isExist = true;
                break;
            }
        }
        if(!isExist){
            System.out.println("User does not exist");
            return -1;
        }
        if(!Objects.equals(userList.get(i).getName(), name)){
            System.out.println("Wrong name");
            return -1;
        }
        System.out.println("Login success");
        return i;

    }


    private static void listTrain(Vector<Line> lineList, Vector<Train> trainList, String[] args) {
        if(args.length == 1) {
            int i;
            int trainInLine = 1;

            for(i=0;i<trainList.size();i++){
                Train train = trainList.get(i);
                Vector<Double> price = train.getTicketPrice();
                Vector<Integer> ticket = train.getTicketNum();
                String trainNum = train.getTrainNum();
                if(trainNum.charAt(0) == '0'){
                    System.out.format("[%d] %s: %s [CC]%.2f:%d [SB]%.2f:%d [GG]%.2f:%d\n",trainInLine, trainNum, train.getLine(), price.get(0), ticket.get(0), price.get(1), ticket.get(1), price.get(2), ticket.get(2));
                }
                else if(trainNum.charAt(0) == 'G'){
                    System.out.format("[%d] %s: %s [SC]%.2f:%d [HC]%.2f:%d [SB]%.2f:%d\n",trainInLine, trainNum, train.getLine(), price.get(0), ticket.get(0), price.get(1), ticket.get(1), price.get(2), ticket.get(2));
                }
                else if(trainNum.charAt(0) == 'K'){
                    System.out.format("[%d] %s: %s [1A]%.2f:%d [2A]%.2f:%d\n",trainInLine, trainNum, train.getLine(), price.get(0), ticket.get(0), price.get(1), ticket.get(1));
                }
                trainInLine++;
            }
            if(trainInLine == 1) {
                System.out.println("No Trains");
            }
            return;
        }
        String lineNum = args[1];
        if(lineExist(lineList, lineNum) == -1) {
            System.out.println("Line does not exist");
            return;
        }
        int i;

        int trainInLine = 1;
        for(i=0;i<trainList.size();i++){
            Train train = trainList.get(i);
            Vector<Double> price = train.getTicketPrice();
            Vector<Integer> ticket = train.getTicketNum();
            String trainNum = train.getTrainNum();
            if(Objects.equals(train.getLine(), lineNum)){
                if(trainNum.charAt(0) == '0'){
                    System.out.format("[%d] %s: %s [CC]%.2f:%d [SB]%.2f:%d [GG]%.2f:%d\n",trainInLine, trainNum, lineNum, price.get(0), ticket.get(0), price.get(1), ticket.get(1), price.get(2), ticket.get(2));
                }
                else if(trainNum.charAt(0) == 'G'){
                    System.out.format("[%d] %s: %s [SC]%.2f:%d [HC]%.2f:%d [SB]%.2f:%d\n",trainInLine, trainNum, lineNum, price.get(0), ticket.get(0), price.get(1), ticket.get(1), price.get(2), ticket.get(2));
                }
                else if(trainNum.charAt(0) == 'K'){
                    System.out.format("[%d] %s: %s [1A]%.2f:%d [2A]%.2f:%d\n",trainInLine, trainNum, lineNum, price.get(0), ticket.get(0), price.get(1), ticket.get(1));
                }
                trainInLine++;
            }

        }
        if(trainInLine == 1) {
            System.out.println("No Trains");
        }
    }

    private static int lineExist(Vector<Line> lineList, String lineNum){
        int i;
        for(i=0;i<lineList.size();i++){
            if(Objects.equals(lineList.get(i).getLine(), lineNum)){
                return i;
            }
        }
        return -1;
    }

    private static void checkTicket(Vector<Line> lineList, Vector<Train> trainList, String[] args) {
        if(args.length < 5) {
            System.out.println("Arguments illegal");
            return;
        }
        String trainNum = args[1];
        if(trainNum.length() < 5){
            System.out.println("Train serial illegal");
            return;
        }
        if(!(isNum(trainNum.charAt(1)) && isNum(trainNum.charAt(2)) && isNum(trainNum.charAt(3)) && isNum(trainNum.charAt(4)))){
            System.out.println("Train serial illegal");
            return;
        }
        int trainIndex = trainExist(trainList, trainNum);
        if( trainIndex == -1){
            System.out.println("Train serial does not exist");
            return;
        }

        Train train = trainList.get(trainIndex);
        String lineName= train.getLine();
        int lineIndex = findLine(lineList, lineName);
        Line line = lineList.get(lineIndex);
        int beginIndex = stationExist(line, args[2]);
        int destIndex = stationExist(line, args[3]);
        if(beginIndex == -1 || destIndex == -1){
            System.out.println("Station does not exist");
            return;
        }
        Vector<Integer> mileage = line.getStationMileage();
        int distance = abs(mileage.get(destIndex) - mileage.get(beginIndex));

        Vector<Integer> ticket = train.getTicketNum();
        Vector<Double> price = train.getTicketPrice();


        if(trainNum.charAt(0) == '0'){
            if(Objects.equals(args[4], "CC")){
                System.out.format("[%s: %s->%s] seat:CC remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(0), distance, distance*price.get(0));
            }
            else if(Objects.equals(args[4], "SB")){
                System.out.format("[%s: %s->%s] seat:SB remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(1), distance, distance*price.get(1));
            }
            else if(Objects.equals(args[4], "GG")){
                System.out.format("[%s: %s->%s] seat:GG remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(2), distance, distance*price.get(2));
            }
            else{
                System.out.println("Seat does not match");
                return;
            }
        }
        else if(trainNum.charAt(0) == 'G'){
            if(Objects.equals(args[4], "SC")){
                System.out.format("[%s: %s->%s] seat:SC remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(0), distance, distance*price.get(0));
            }
            else if(Objects.equals(args[4], "HC")){
                System.out.format("[%s: %s->%s] seat:HC remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(1), distance, distance*price.get(1));
            }
            else if(Objects.equals(args[4], "SB")){
                System.out.format("[%s: %s->%s] seat:SB remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(2), distance, distance*price.get(2));
            }
            else{
                System.out.println("Seat does not match");
                return;
            }
        }
        else if(trainNum.charAt(0) == 'K'){
            if(Objects.equals(args[4], "1A")){
                System.out.format("[%s: %s->%s] seat:1A remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(0), distance, distance*price.get(0));
            }
            else if(Objects.equals(args[4], "2A")){
                System.out.format("[%s: %s->%s] seat:2A remain:%d distance:%d price:%.2f\n",trainNum, args[2], args[3], ticket.get(1), distance, distance*price.get(1));
            }
            else{
                System.out.println("Seat does not match");
                return;
            }
        }
    }

    private static void delTrain(Vector<Line> lineList, Vector<Train> trainList, String[] args) {
        if(args.length < 2) {
            System.out.println("Arguments illegal");
            return;
        }
        String trainNum = args[1];
        int exist = trainExist(trainList, trainNum);
        if(exist== -1){
            System.out.println("Train does not exist");
            return;
        }
        else{
            Train train = trainList.get(exist);
            String lineNum = train.getLine();
            int lineIndex = lineExist(lineList, lineNum);
            Line line = lineList.get(lineIndex);
            int used = line.getUsedCapacity();
            used--;
            line.setUsedCapacity(used);
            trainList.remove(exist);

            System.out.println("Del Train Success");
        }
    }

    private static int checkTrainSerial(String trainNum, Train train, int num){
        int len = trainNum.length();
        if(len!=5){
            System.out.println("Train serial illegal");
            return 0;
        }
        if(!(isNum(trainNum.charAt(1)) && isNum(trainNum.charAt(2)) && isNum(trainNum.charAt(3)) && isNum(trainNum.charAt(4)))){
            System.out.println("Train serial illegal");
            return 0;
        }

        if(num == 7) {
            if(trainNum.charAt(0) == 'K'){
                train.setTrainType("Koya");
            }
            else if(trainNum.charAt(0) == '0'||trainNum.charAt(0) == 'G'){
                System.out.println("Arguments illegal");
                return 0;
            }
            else{
                System.out.println("Train serial illegal");
                return 0;
            }
        }
        else if (num == 9) {
            if (trainNum.charAt(0) == '0') {
                train.setTrainType("Normal");
            } else if (trainNum.charAt(0) == 'G') {
                train.setTrainType("Gatimaan");
            }
            else if(trainNum.charAt(0) == 'K'){
                System.out.println("Arguments illegal");
                return 0;
            }else {
                System.out.println("Train serial illegal");
                return 0;
            }
        }
        return 1;
    }

    private static void addTrain(Vector<Line> lineList, Vector<Train> trainList, String[] args) {
        int num = args.length;
        if(num!=7 && num!= 9) {
            System.out.println("Arguments illegal");
            return;
        }
        String trainNum = args[1];

        Train train = new Train();
        if(checkTrainSerial(trainNum, train, num) == 0){
            return;
        }

        if(trainExist(trainList, trainNum) != -1){
            System.out.println("Train serial duplicate");
            return;
        }

        train.setTrainNum(trainNum);

        String lineName = args[2];
        int i = findLine(lineList, lineName);

        if(i == -1){
            System.out.println("Line illegal");
            return;
        }

        train.setLine(lineName);

        Line line = lineList.get(i);
        int used = line.getUsedCapacity();
        int capacity = line.getCapacity();
        if(used>=capacity){
            System.out.println("Line illegal");
            return;
        }

        Vector<Double> ticketPrice = train.getTicketPrice();
        if(!(isDouble(args[3]) && isDouble(args[5]))){
            System.out.println("Price illegal");
            return;
        }
        if(Double.valueOf(args[3])<0 || Double.valueOf(args[5])<0){
            System.out.println("Price illegal");
            return;
        }
        ticketPrice.addElement(Double.valueOf(args[3]));
        ticketPrice.addElement(Double.valueOf(args[5]));
        train.setTicketPrice(ticketPrice);

        if(num == 9) {
            if (!isDouble(args[7])) {
                System.out.println("Price illegal");
                return;
            }
            if (Double.valueOf(args[7]) < 0) {
                System.out.println("Price illegal");
                return;
            }

            ticketPrice = train.getTicketPrice();
            ticketPrice.addElement(Double.valueOf(args[7]));
            train.setTicketPrice(ticketPrice);
        }



        if(!(isInt(args[4]) && isInt(args[6]))){
            System.out.println("Ticket num illegal");
            return;
        }

        Vector<Integer> ticketNum = train.getTicketNum();
        if(Integer.valueOf(args[4])<0 || Integer.valueOf(args[6])<0){
            System.out.println("Ticket num illegal");
            return;
        }
        ticketNum.addElement(Integer.valueOf(args[4]));
        ticketNum.addElement(Integer.valueOf(args[6]));
        train.setTicketNum(ticketNum);

        if (num == 9){
            if(!isInt(args[8])){
                System.out.println("Ticket num illegal");
                return;
            }
            if(Integer.valueOf(args[8])<0){
                System.out.println("Ticket num illegal");
                return;
            }
            ticketNum = train.getTicketNum();
            ticketNum.addElement(Integer.valueOf(args[8]));
            train.setTicketNum(ticketNum);
        }
        used++;
        line.setUsedCapacity(used);
        // 按列车号添加
        if(trainList.isEmpty()){
            trainList.addElement(train);
            System.out.println("Add Train Success");
            return;
        }
        for(i=0;i<trainList.size();i++){
            Train train2 = trainList.get(i);
            String train2Num = train2.getTrainNum();
            if(trainNum.charAt(0)>train2Num.charAt(0)){
                trainList.add(i, train);
                break;
            }
            else if(trainNum.charAt(0)<train2Num.charAt(0)){
                continue;
            }
            else if(trainNum.compareTo(train2Num)<0){
                trainList.add(i, train);
                break;
            }
        }
        if(i==trainList.size()){
            trainList.add(i, train);
        }

        System.out.println("Add Train Success");
    }

    private static int trainExist(Vector<Train> trainList, String trainNum) {
        for (int i = 0; i < trainList.size(); i++) {
            Train train = trainList.get(i);
            if(Objects.equals(train.getTrainNum(), trainNum)){
                return i;
            }
        }
        return -1;
    }

    private static boolean isNum(char charAt) {
        if(charAt>='0'&&charAt<='9') {
            return true;
        } else {
            return false;
        }
    }

    private static void listLine(Vector<Line> lineList){
        int i;
        if(lineList.isEmpty()){
            System.out.println("No Lines");
            return;
        }
        for(i=0;i<lineList.size();i++){
            Line line = lineList.get(i);
            line.toString(i+1);
        }
    }

    private static void lineInfo(Vector<Line> lineList, String[] args) {
        if(args.length < 2) {
            System.out.println("Arguments illegal");
            return;
        }
        int index = findLine(lineList, args[1]);
        if( index == -1){
            System.out.println("Line does not exist");
        }
        else{
            lineList.get(index).toString();
        }
    }

    private static void addStation(Vector<Line> lineList, String[] args) {
        if(args.length < 4) {
            System.out.println("Arguments illegal");
            return;
        }
        if(!isInt(args[3])){
            System.out.println("Arguments illegal");
            return;
        }
        if(Integer.valueOf(args[3])<0){
            System.out.println("Arguments illegal");
            return;
        }
        int i = findLine(lineList, args[1]);
        int len = args.length;
        if(i == -1){
            System.out.println("Line does not exist");
        }
        else{
            Line line = lineList.get(i);
            if(stationExist(line, args[2]) != -1){
                System.out.println("Station duplicate");
            }
            else{
                int position = stationPosition(line, Integer.parseInt(args[3]));
                line.station.add(position, args[2]);
                line.stationMileage.add(position, Integer.valueOf(args[3]));
                System.out.println("Add Station success");
            }
        }
    }

    private static int stationPosition(Line line, int mileage) {
        Vector<Integer> stationMileage = line.getStationMileage();
        int i;
        for(i=0;i<stationMileage.size();i++){
            if(mileage<stationMileage.get(i)){
                return i;
            }
        }
        return stationMileage.size();
    }

    private static void delStation(Vector<Line> lineList, String[] args){
        if(args.length < 3) {
            System.out.println("Arguments illegal");
            return;
        }
        int i = findLine(lineList, args[1]);
        int j = stationExist(lineList.get(i), args[2]);
        if( j != -1){
            Line line = lineList.get(i);
            line.getStation().remove(j);
            line.getStationMileage().remove(j);
            System.out.println("Delete Station success");
        }
        else{
            System.out.println("Station does not exist");
        }
    }

    private static void delLine(Vector<Line> lineList, String[] args){
        if(args.length < 2){
            System.out.println("Arguments illegal");
            return;
        }
        for (int i = 0; i < lineList.size(); i++) {
            Line line = lineList.get(i);
            if(Objects.equals(line.getLine(), args[1])){
                lineList.remove(i);
                System.out.println("Del Line success");
                return;
            }
        }
        System.out.println("Line does not exist");
    }

    private static void addLine(Vector<Line> lineList, String[] args){
        int num = (args.length - 3) / 2;
        if(args.length % 2==0){
            System.out.println("Arguments illegal");
            return;
        }
        if(args.length < 3) {
            System.out.println("Arguments illegal");
            return;
        }
        if(lineList.isEmpty()){

        }
        else if(findLine(lineList, args[1]) != -1){
            System.out.println("Line already exists");
            return;
        }

        if(isInt(args[2]) == false) {
            System.out.println("Arguments illegal");
            return;
        }
        if(Integer.parseInt(args[2]) <= 0) {
            System.out.println("Capacity illegal");
            return;
        }
        Line line = new Line();
        line.setLine(args[1]);
        line.setCapacity(Integer.parseInt(args[2]));
        int i;
        for(i=1; i<=num; i++){
            if(isInt(args[2*i+2]) == false) {
                System.out.println("Arguments illegal");
                break;
            }
            else if(stationExist(line, args[2*i+1]) != -1){
                System.out.println("Station duplicate");
                break;
            }
            else {
                int j;
                Vector<Integer> mileage = line.getStationMileage();
                for(j=0;j<mileage.size();j++){
                    if(Integer.parseInt(args[2*i+2])<mileage.get(j)){
                        line.addStation(line, j, args[2*i+1], Integer.parseInt(args[2*i+2]));
                        break;
                    }
                }
                if(j==mileage.size()){
                    line.addStation(line, j, args[2*i+1], Integer.parseInt(args[2*i+2]));
                }

            }
        }
        if(i == num+1) {
            int j;
            String lineName = line.getLine();
            if(lineList.isEmpty()){
                lineList.addElement(line);
            }
            else{
                for(j=0;j<lineList.size();j++){
                    Line line2 = lineList.get(j);
                    if(lineName.compareTo(line2.getLine())<0){
                        lineList.add(j, line);
                        break;
                    }
                }
                if(j==lineList.size()){
                    lineList.add(j, line);
                }
            }

            System.out.println("Add Line success");
        }
        return;
    }

    private static int findLine(Vector<Line> lineList, String lineName){
        for (int i = 0; i < lineList.size(); i++) {
            Line line = lineList.get(i);
            if(Objects.equals(line.getLine(), lineName)){
                return i;
            }
        }
        return -1;
    }

    private static int stationExist(Line line, String stationName){
        Vector<String> station = line.getStation();
        return station.indexOf(stationName);
    }

    public static boolean isInt(String str){
        try {
            int num=Integer.valueOf(str);
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    public static boolean isDouble(String str){
        try {
            double num=Double.valueOf(str);
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    public static void addUser(Vector<User> userList, String[] splited){
        if(splited.length == 4){
            User user = new User();
            User.setString(userList, user, splited);
            return;
        }
        else if(splited.length == 5){
            Student student = new Student();
            student.setDiscountNum(Integer.parseInt(splited[4]));
            Student.setString(userList, student, splited);
            return;
        }
        return;
    }

}

class Train{
    private String trainType;
    private String trainNum;
    private String line;
    private Vector<Double> ticketPrice = new Vector<Double>();
    private Vector<Integer> ticketNum = new Vector<Integer>();

    public String getLine() {
        return line;
    }

    public void setLine(String line) {
        this.line = line;
    }

    public String getTrainNum() {
        return trainNum;
    }

    public void setTrainNum(String trainNum) {
        this.trainNum = trainNum;
    }

    public String getTrainType() {
        return trainType;
    }

    public void setTrainType(String trainType) {
        this.trainType = trainType;
    }

    public Vector<Double> getTicketPrice() {
        return ticketPrice;
    }

    public void setTicketPrice(Vector<Double> ticketPrice) {
        this.ticketPrice = ticketPrice;
    }

    public Vector<Integer> getTicketNum() {
        return ticketNum;
    }

    public void setTicketNum(Vector<Integer> ticketNum) {
        this.ticketNum = ticketNum;
    }
}

class Station{
    private String name;

    public String getName(){
        return name;
    }

    public void setName(String stationName){
        name = stationName;
    }
}

class Line{
    private String line;
    private int capacity;
    public Vector<String> station = new Vector<String>();
    public Vector<Integer> stationMileage = new Vector<Integer>();
    private int usedCapacity;

    public String toString(){
        System.out.format("%s %d/%d",getLine(), getUsedCapacity(), getCapacity());
        int i;
        for(i=0;i< station.size();i++){
            System.out.format(" %s:%d",getStation().get(i),getStationMileage().get(i));
        }
        System.out.print("\n");
        return null;
    }

    public String toString(int index){
        System.out.format("[%d] %s %d/%d",index, getLine(), getUsedCapacity(), getCapacity());
        int i;
        for(i=0;i< station.size();i++){
            System.out.format(" %s:%d",getStation().get(i),getStationMileage().get(i));
        }
        System.out.print("\n");
        return null;
    }

    public int getUsedCapacity() {
        return usedCapacity;
    }

    public void setUsedCapacity(int usedCapacity) {
        this.usedCapacity = usedCapacity;
    }

    public Vector<String> getStation(){
        return station;
    }

    public Vector<Integer> getStationMileage(){
        return stationMileage;
    }

    public void setStation(String name, int mileage){
        station.addElement(name);
        stationMileage.addElement(mileage);
    }

    public String getLine(){
        return line;
    }

    public void setLine(String lineName){
        line = lineName;
    }

    public int getCapacity(){
        return capacity;
    }

    public void setCapacity(int lineCapacity){
        capacity = lineCapacity;
    }

    public void addStation(Line line, int index, String stationName, int mileage){
        line.station.add(index, stationName);
        line.stationMileage.add(index, mileage);
    }
}

class Order{
    private String trainNum;
    private String begin;
    private String destination;
    private String seat;
    private int seatNum;
    private double single;
    private double sum;
    private boolean paid = false;


    public double getSingle() {
        return single;
    }

    public void setSingle(double single) {
        this.single = single;
    }

    public boolean isPaid() {
        return paid;
    }

    public void setPaid(boolean paid) {
        this.paid = paid;
    }

    public String getTrainNum() {
        return trainNum;
    }

    public int getSeatNum() {
        return seatNum;
    }

    public String getBegin() {
        return begin;
    }

    public String getDestination() {
        return destination;
    }

    public String getSeat() {
        return seat;
    }

    public double getSum() {
        return sum;
    }

    public void setTrainNum(String trainNum) {
        this.trainNum = trainNum;
    }
    public void setBegin(String begin) {
        this.begin = begin;
    }

    public void setDestination(String destination) {
        this.destination = destination;
    }

    public void setSeat(String seat) {
        this.seat = seat;
    }

    public void setSeatNum(int seatNum) {
        this.seatNum = seatNum;
    }

    public void setSum(double sum) {
        this.sum = sum;
    }
}

class User {
    private String name;
    private String sex;
    private String Aadhaar;
    private boolean admin;
    private boolean isLogin;
    private double balance = 0;
    Vector<Order> orders = new Vector<Order>();
    private boolean cert = true;

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    public boolean isCert() {
        return cert;
    }

    public void setCert(boolean cert) {
        this.cert = cert;
    }

    public Vector<Order> getOrders() {
        return orders;
    }

    public void setOrders(Vector<Order> orders) {
        this.orders = orders;
    }

    public boolean isLogin() {
        return isLogin;
    }

    public void setLogin(boolean login) {
        isLogin = login;
    }

    public boolean getAdmin() {
        return admin;
    }

    public void setAdmin(boolean isAdmin) {
        admin = isAdmin;
    }

    public String getName() {
        return name;
    }

    public void setName(String myName) {
        name = myName;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String mySex) {
        sex = mySex;
    }

    public String getAadhaar() {
        return Aadhaar;
    }

    public void setAadhaar(String myAadhaar) {
        Aadhaar = myAadhaar;
    }

    @Override
    public String toString() {
        System.out.println("Name:" + getName());
        System.out.println("Sex:" + getSex());
        System.out.println("Aadhaar:" + getAadhaar());
        return null;
    }

    public static boolean str(String str) {
        final String number = "0123456789";
        char[] ch = str.toCharArray();
        if (ch.length == 0) {
            return false;
        } else {
            boolean is = true;
            if (number.indexOf(ch[0]) == -1) {
                for (int i = 0; i < ch.length; i++) {
                    if ((ch[i] >= 65 && ch[i] <= 90) || (ch[i] >= 97 && ch[i] <= 122) || ch[i] == 95) {
                    } else {
                        is = false;
                    }
                }
                return is;
            } else {
                return false;
            }
        }
    }

    public static boolean card(String cardNum, String sex) {
        char[] ch = cardNum.toCharArray();
        boolean is = true;
        int num1 = (ch[0] - '0') * 1000 + (ch[1] - '0') * 100 + (ch[2] - '0') * 10 + (ch[3] - '0');
        int num2 = (ch[4] - '0') * 1000 + (ch[5] - '0') * 100 + (ch[6] - '0') * 10 + (ch[7] - '0');
        int num3 = (ch[8] - '0') * 100 + (ch[9] - '0') * 10 + (ch[10] - '0');
        if (num1 == 0 || num1 > 1237) {
            is = false;
        }
        if (num2 < 20 || num2 > 460) {
            is = false;
        }
        if (num3 > 100) {
            is = false;
        }
        if (Objects.equals(sex, "F") && (ch[11] - '0') != 0) {
            is = false;
        }
        if (Objects.equals(sex, "M") && (ch[11] - '0') != 1) {
            is = false;
        }
        if (Objects.equals(sex, "O") && (ch[11] - '0') != 2) {
            is = false;
        }
        return is;
    }

    public static boolean repeat(Vector<User> userlist, String cardNum) {
        boolean is = true;
        for(int i=0;i< userlist.size();i++) {
            if (Objects.equals(userlist.get(i).getAadhaar(), cardNum)) {
                return true;
            }
        }
        return false;
    }

    public static void setString(Vector<User> userlist, User user, String[] args) {
        // System.out.println(args.length);
        if (args.length != 4) {
            System.out.println("Arguments illegal");
        } else {
            // System.out.println(args[2]);
            if (!str(args[1])) {
                System.out.println("Name illegal");
            } else if (!Objects.equals(args[2], "F") && !Objects.equals(args[2], "M") && !Objects.equals(args[2], "O")) {
                System.out.println("Sex illegal");
            } else if (!card(args[3], args[2])) {
                System.out.println("Aadhaar number illegal");
            } else if (repeat(userlist, args[3])) {
                System.out.println("Aadhaar number exist");
            } else {
                user.setName(args[1]);
                user.setSex(args[2]);
                user.setAadhaar(args[3]);
                user.setAdmin(false);
                user.setLogin(false);
                userlist.addElement(user);
                user.toString();

            }

        }
    }
}

class Student extends User {
    private int discountNum;

    public void setDiscountNum(int discountNum) {
        this.discountNum = discountNum;
    }

    public int getDiscountNum() {
        return discountNum;
    }

    @Override
    public String toString() {
        System.out.println("Name:" + getName());
        System.out.println("Sex:" + getSex());
        System.out.println("Aadhaar:" + getAadhaar());
        System.out.println("Discount:" + getDiscountNum());
        return null;
    }

    public static void setString(Vector<User> userlist, User user, String[] args) {
        // System.out.println(args.length);
        if (args.length != 5) {
            System.out.println("Arguments illegal");
        } else {
            // System.out.println(args[2]);
            if (!str(args[1])) {
                System.out.println("Name illegal");
            } else if (!Objects.equals(args[2], "F") && !Objects.equals(args[2], "M") && !Objects.equals(args[2], "O")) {
                System.out.println("Sex illegal");
            } else if (!card(args[3], args[2])) {
                System.out.println("Aadhaar number illegal");
            } else if (repeat(userlist, args[3])) {
                System.out.println("Aadhaar number exist");
            } else {
                user.setName(args[1]);
                user.setSex(args[2]);
                user.setAadhaar(args[3]);
                user.setAdmin(false);
                user.setLogin(false);
                userlist.addElement(user);
                user.toString();

            }

        }
    }
}

class Cert{
    public static boolean isFile(String fileName){
        try {
            BufferedReader br = new BufferedReader(new FileReader(fileName));
            return true;
        } catch (Exception e) {
            return false;
        }
    }
    public static HashMap<String, Boolean> read(HashMap<String, Boolean> cert, String fileName, Vector<User> userList) throws IOException {
        // fileName = "CTS1\\" + fileName;
        if(isFile((fileName))){
            try {
                BufferedReader br = new BufferedReader(new FileReader(fileName));
                String line;
                while ((line = br.readLine()) != null) {
                    String[] lines = line.split(",");
                    cert.put(lines[0], lines[1].equals("P"));
                }
                br.close();
            }
            catch(IOException e){
                throw new RuntimeException();
            }
            Iterator<Map.Entry<String, Boolean>> iterator = cert.entrySet().iterator();
            int positive = 0, negative = 0;
            while (iterator.hasNext()) {
                Map.Entry<String, Boolean> entry = iterator.next();
                if(entry.getValue()){
                    positive++;
                }
                else{
                    negative++;
                }
                String userCard = entry.getKey();
                for(int i=0;i<userList.size();i++){
                    User user = userList.get(i);
                    if(Objects.equals(userCard, user.getAadhaar())){
                        user.setCert(entry.getValue());
                        break;
                    }
                }
                // System.out.print(entry.getValue());
            }
            System.out.format("Import Success, Positive:%d Negative:%d\n",positive,negative);
        }
        else {
            System.out.println("no such file");
        }
        return cert;
    }


}

```

