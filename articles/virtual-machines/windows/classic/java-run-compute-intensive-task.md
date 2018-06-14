---
title: Aplikace Java náročné na virtuálním počítači
description: Naučte se vytvářet Azure virtuální počítač, který spouští aplikaci Java náročné, která lze sledovat v jiné aplikaci Java.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e8da296c30f1d2be3c637e456e90d2f93da67548
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528456"
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Jak spouštět úlohy náročné na výpočetní výkon v Javě na virtuálním počítači
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

S Azure můžete pro zpracování úlohy náročné na výkon virtuálního počítače. Virtuální počítač můžete například zpracování úlohy a výsledky doručení do klientského počítače nebo mobilní aplikace. Po přečtení tohoto článku, budete mít představu o tom, jak vytvořit virtuální počítač, který spouští aplikaci Java náročné, která lze sledovat v jiné aplikaci Java.

Tento kurz předpokládá vědět, jak k vytvoření konzolové aplikace Java, můžete importovat knihovny do aplikace v jazyce Java a může generovat Java archivu (JAR). Předpokládají se žádné znalosti ve službě Microsoft Azure.

Co se dozvíte:

* Postup vytvoření virtuálního počítače s Java Development Kit (JDK) už nainstalovaná.
* Jak se vzdáleně přihlaste k virtuálnímu počítači.
* Jak vytvořit obor názvů sběrnice.
* Jak vytvořit aplikaci Java, která provede náročné úlohy.
* Jak vytvořit aplikaci Java, která monitoruje průběh úlohy náročné na výkon.
* Postup spuštění aplikací Java.
* Postup zastavení aplikací Java.

V tomto kurzu použije Traveling prodejce problém pro úlohy náročné na výkon. Následuje příklad aplikace Java spuštění úlohy náročné na výkon.

![Řešitel problém Traveling prodejce][solver_output]

Následuje příklad monitorování úlohy náročné na výkon aplikací Java.

![Problém Traveling prodejce klienta][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Vytvoření virtuálního počítače
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek**, klikněte na tlačítko **výpočetní**, klikněte na tlačítko **virtuálního počítače**a potom klikněte na **z Galerie**.
3. V **vyberte bitovou kopii virtuálního počítače** dialogové okno, vyberte **JDK 7 Windows serveru 2012**.
   Všimněte si, že **JDK 6 systému Windows Server 2012** je k dispozici v případě, že máte starší aplikace, které ještě nejsou připravené ke spuštění v JDK 7.
4. Klikněte na **Další**.
5. V **konfigurace virtuálního počítače** dialogové okno:
   1. Zadejte název pro virtuální počítač.
   2. Zadejte velikost používat pro virtuální počítač.
   3. Zadejte název pro správce v **uživatelské jméno** pole. Nezapomeňte toto jméno a heslo, které budou vedle zadat, je budou používat při vzdálené přihlášení k virtuálnímu počítači.
   4. Zadejte heslo do **nové heslo** pole a zadejte jej v znovu **potvrdit** pole. Toto je heslo k účtu správce.
   5. Klikněte na **Další**.
6. V dalším **konfigurace virtuálního počítače** dialogové okno:
   1. Pro **Cloudová služba**, použijte výchozí **vytvořte novou cloudovou službu**.
   2. Hodnota **název cloudové služby DNS** musí být jedinečný v rámci cloudapp.net. V případě potřeby upravte tuto hodnotu tak, že Azure označuje, že je jedinečný.
   3. Zadejte oblast, skupiny vztahů nebo virtuální sítě. Pro účely tohoto kurzu, zadejte v oblasti **západní USA**.
   4. Pro **účet úložiště**, vyberte **použít účet úložiště automaticky generované**.
   5. Pro **sadu dostupnosti**, vyberte **(None)**.
   6. Klikněte na **Další**.
7. V konečné **konfigurace virtuálního počítače** dialogové okno:
   1. Přijměte výchozí koncový bod položky.
   2. Klikněte na **Dokončit**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Vzdáleně se přihlásit k virtuálnímu počítači
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **virtuální počítače**.
3. Klikněte na název virtuálního počítače, který chcete k přihlášení do.
4. Klikněte na **Připojit**.
5. Reagujete na výzvy podle potřeby pro připojení k virtuálnímu počítači. Pokud budete vyzváni k správce jména a hesla, použijte hodnoty, které jste zadali při vytváření virtuálního počítače.

Všimněte si, že funkci Azure Service Bus vyžaduje certifikát Baltimore CyberTrust Root, který chcete nainstalovat jako součást vašeho prostředí JRE **cacerts** uložit. Tento certifikát je automaticky zahrnuty v prostředí Java Runtime (JRE) používané v tomto kurzu. Pokud jste tento certifikát ve vašem prostředí JRE **cacerts** úložiště najdete v tématu [přidání certifikátu do úložiště certifikátů certifikační Autority Java] [ add_ca_cert] informace o přidání (a také informace o zobrazení certifikáty v úložišti cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Postup vytvoření oboru názvů service bus
Pokud chcete začít používat fronty služby Service Bus v Azure, musíte nejdřív vytvořit obor názvů služby. Obor názvů poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů služby:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně portálu Azure, klikněte na tlačítko **Service Bus, řízení přístupu a ukládání do mezipaměti**.
3. V levém podokně portálu Azure, klikněte na **Service Bus** uzel a klikněte **nový** tlačítko.  
   ![Snímek obrazovky uzel Service Bus][svc_bus_node]
4. V **vytvoření nové služby Namespace** dialogovém okně zadejte **Namespace**a pokud chcete mít jistotu, že je jedinečný, klikněte **zkontrolovat dostupnost** tlačítko.  
   ![Vytvořit nový Namespace snímek][create_namespace]
5. Po dostupné zajistit, že název oboru názvů vyberte zemi nebo oblast, ve kterém by měl být hostován vašeho oboru názvů a klikněte **vytvořit Namespace** tlačítko.  
   
   Obor názvů, který jste vytvořili se potom zobrazí na portálu Azure a aktivovat chvíli trvá. Počkejte, dokud je stav **Active** před pokračováním na další krok.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Získání výchozí pověření pro správu oboru názvů
Aby bylo možné provádět operace správy, jako je například vytváření fronty, na novém oboru názvů, potřebujete získat přihlašovací údaje pro obor názvů správu.

1. V levém navigačním podokně klikněte **Service Bus** uzlu zobrazíte seznam dostupných oborů názvů.
   ![K dispozici snímek obory názvů][avail_namespaces]
2. Vyberte obor názvů, který jste právě vytvořili, v zobrazeném seznamu.
   ![Snímek obrazovky seznamu Namespace][namespace_list]
3. Pravém **vlastnosti** podokně jsou uvedeny vlastnosti pro nový obor názvů.
   ![Snímek obrazovky podokna Vlastnosti][properties_pane]
4. **Výchozí klíč** skryt. Klikněte **zobrazení** tlačítko pro zobrazení zabezpečovací přihlašovací údaje.
   ![Výchozí klíč – snímek obrazovky][default_key]
5. Poznamenejte si **výchozí vystavitele** a **výchozí klíč** jako tento níže uvedené informace se používají k provádění operací s oborem názvů.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Jak vytvořit aplikaci Java, která provede náročné úlohy
1. Na vývojovém počítači (která nemusí být virtuální počítač, který jste vytvořili) stáhněte si [Azure SDK pro jazyk Java](https://azure.microsoft.com/develop/java/).
2. Vytvořte konzolovou aplikaci Java pomocí ukázkový kód na konci této části. V tomto kurzu použijeme **TSPSolver.java** jako název souboru Java. Změnit **vaše\_služby\_sběrnice\_obor názvů**, **vaše\_služby\_sběrnice\_vlastníka**a **vaše\_služby\_sběrnice\_klíč** zástupné symboly pro použití služby service bus **obor názvů**, **výchozí vystavitele** a  **Výchozí klíč** hodnoty v uvedeném pořadí.
3. Po kódování, exportovat aplikaci spustitelného archivu Java (JAR) a balíček požadované knihovny do vygenerované JAR. V tomto kurzu použijeme **TSPSolver.jar** jako vygenerovaný název JAR.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Jak vytvořit aplikaci Java, která monitoruje průběh úlohy náročné na výkon
1. Na vývojovém počítači vytvořte konzolovou aplikaci Java pomocí ukázkový kód na konci této části. V tomto kurzu použijeme **TSPClient.java** jako název souboru Java. Jak je uvedeno výše, upravit **vaše\_služby\_sběrnice\_obor názvů**, **vaše\_služby\_sběrnice\_vlastníka**, a **vaše\_služby\_sběrnice\_klíč** zástupné symboly pro použití služby service bus **obor názvů**, **výchozí vystavitele**a **výchozí klíč** hodnoty v uvedeném pořadí.
2. Exportujte aplikace spustitelného JAR a balíček požadované knihovny do vygenerované JAR. V tomto kurzu použijeme **TSPClient.jar** jako vygenerovaný název JAR.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Spuštění aplikace Java
Spuštění aplikace náročné na výkon, nejprve k vytvoření fronty, potom k vyřešení problému Saleseman cestě, která přidá aktuální nejlepší směrování do fronty service bus. Při spuštění (nebo později) aplikace náročné spuštění klienta zobrazit výsledky z fronty service bus.

### <a name="to-run-the-compute-intensive-application"></a>Ke spuštění aplikace náročné na výkon
1. Přihlaste se k virtuálnímu počítači.
2. Vytvořte složku, kde budete spouštět aplikace. Například **c:\TSP**.
3. Kopírování **TSPSolver.jar** k **c:\TSP**,
4. Vytvořte soubor s názvem **c:\TSP\cities.txt** s tímto obsahem.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. Na příkazovém řádku změňte adresáře na c:\TSP.
6. Zkontrolujte, zda složky Bin přejít prostředí JRE v proměnné prostředí PATH.
7. Budete muset vytvořit frontou service bus před spuštěním permutací solver TSP. Spusťte následující příkaz k vytvoření fronty service bus.
   
        java -jar TSPSolver.jar createqueue
8. Teď, když je vytvářena fronta, můžete spustit permutací solver TSP. Například spusťte následující příkaz ke spuštění Řešitele pro 8 měst.
   
        java -jar TSPSolver.jar 8
   
   Pokud nezadáte číslo, bude spuštěna pro 10 měst. Jako Řešitele vyhledá aktuální nejkratší trasy, přidá je do fronty.

> [!NOTE]
> Čím větší číslo, které zadáte, tím déle solver spustí. Například běžet 14 města může trvat několik minut a spuštěna 15 města může trvat několik hodin. Zvýšení na 16 nebo víc měst může mít za následek dnů modulu runtime (nakonec týdny, měsíce a letopočty). Příčinou je rychlý nárůst počtu permutací pomocí Řešitele vyhodnotit jako číslo zvyšuje měst.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Spuštění monitorování klientské aplikace
1. Přihlaste se k počítači, kde budete spouštět klientské aplikace. Toto nemusí být stejný počítač s **TSPSolver** aplikace, i když může být.
2. Vytvořte složku, kde budete spouštět aplikace. Například **c:\TSP**.
3. Kopírování **TSPClient.jar** k **c:\TSP**,
4. Zkontrolujte, zda složky Bin přejít prostředí JRE v proměnné prostředí PATH.
5. Na příkazovém řádku změňte adresáře na c:\TSP.
6. Spusťte následující příkaz.
   
        java -jar TSPClient.jar
   
    Volitelně můžete zadejte počet minut do režimu spánku mezi kontroly fronty předáním v argument příkazového řádku. Doba spánku výchozí kontroly fronty je 3 minuty, který je použit, pokud je předán žádný argument příkazového řádku **TSPClient**. Pokud chcete použít jiné hodnoty pro interval režimu spánku, například jednu minutu, spusťte následující příkaz.
   
        java -jar TSPClient.jar 1
   
    Klient se spustí, dokud ho uvidí zprávu fronty služby "Dokončeno". Poznámka: Pokud vícenásobné výskyty Řešitele spustíte bez spuštění klienta, pravděpodobně muset spustit klienta vícekrát zcela vyprázdnit frontu. Alternativně můžete odstranit a vytvořit znovu. Pokud chcete odstranit frontu, spusťte následující **TSPSolver** (ne **TSPClient**) příkaz.
   
        java -jar TSPSolver.jar deletequeue
   
    Řešitele se spustí, dokud neskončí zkoumání všechny trasy.

## <a name="how-to-stop-the-java-applications"></a>Postup zastavení aplikací Java
Řešitel i klientské aplikace, můžete stisknout **Ctrl + C** ukončíte, pokud chcete ukončit před dokončením normální.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
