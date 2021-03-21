---
title: Protokolování toku síťového provozu do a z virtuálního počítače – kurz – Azure Portal | Microsoft Docs
description: Zjistěte, jak protokolovat tok síťového provozu do a z virtuálního počítače pomocí funkce protokolů toku NSG služby Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 385d43e46cd3f9465c0fbf9a02eeae356f48fac4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966526"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Kurz: Protokolování síťového provozu do a z virtuálního počítače pomocí portálu Azure Portal

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)

Skupina zabezpečení sítě (NSG) umožňuje filtrovat příchozí provoz do a odchozí provoz z virtuálního počítače. Můžete protokolovat síťový provoz procházející skupinou zabezpečení sítě pomocí funkce protokolu toku NSG služby Network Watcher. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač se skupinou zabezpečení sítě
> * Povolit Network Watcher a zaregistrovat poskytovatele Microsoft.Insights
> * Povolit protokol toku provozu pro NSG pomocí funkce protokolu toku NSG služby Network Watcher
> * Stáhnout data protokolu
> * Zobrazit data protokolu

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **COMPUTE** a potom vyberte **Windows Server 2016 Datacenter** nebo verzi **Ubuntu serveru**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte **myResourceGroup**.|
    |Umístění| Vyberte **východní USA**|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** přijměte všechny výchozí hodnoty a vyberte **OK**.
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače. Nasazení virtuálního počítače trvá několik minut. Než budete pokračovat ve zbývajících krocích, počkejte, až virtuální počítač dokončí nasazování.

Vytvoření virtuálního počítače trvá několik minut. Se zbývajícími kroky nepokračujte, dokud se vytvoření virtuálního počítače nedokončí. Když portál vytvoří virtuální počítač, vytvoří také skupinu zabezpečení sítě s názvem **myVM-nsg** a přidruží ji k síťovému rozhraní virtuálního počítače.

## <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud už máte sledovací proces sítě v oblasti USA – východ povolený, přejděte na [Registrace poskytovatele Insights](#register-insights-provider).

1. Na webu Azure Portal vyberte **Všechny služby**. Do **pole filtru** zadejte *Network Watcher*. Pokud se ve výsledcích zobrazí **Network Watcher** , vyberte ji.
2. Rozbalte **Oblasti** a potom vedle **USA – východ** vyberte **...** (stejně jako v následujícím obrázku):

    ![Povolení Network Watcheru](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Vyberte **Povolit Network Watcher**.

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Protokolování toku NSG vyžaduje poskytovatele **Microsoft.Insights**. Poskytovatele zaregistrujete pomocí těchto kroků:

1. V levé horním rohu portálu vyberte **Všechny služby**. Do pole Filtr zadejte *Předplatná*. Když se **Předplatná** zobrazí ve výsledcích hledání, vyberte je.
2. Ze seznamu předplatných vyberte předplatné, pro které chcete poskytovatele povolit.
3. Vyberte **Poskytovatelé prostředků** v **NASTAVENÍ**.
4. Zkontrolujte, jestli **STAV** poskytovatele **microsoft.insights** je **Zaregistrováno**, jak je vidět na následujícím obrázku. Pokud je stav **Neregistrováno**, pak vpravo od poskytovatele vyberte **Zaregistrovat**.

    ![Registrace poskytovatele](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Povolení protokolů toku NSG

1. Data protokolu toku NSG se zapisují do účtu Azure Storage. Pokud chcete vytvořit účet Azure Storage, v levém horním rohu portálu vyberte **+ Vytvořit prostředek**.
2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Zadejte nebo vyberte následující informace, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit**.

    | Nastavení        | Hodnota                                                        |
    | ---            | ---   |
    | Název           | Délka 3–24 znaků, může obsahovat jenom malá písmena a čísla a musí být jedinečný v rámci všech účtů Azure Storage.                                                               |
    | Umístění       | Vyberte **východní USA**                                           |
    | Skupina prostředků | Vyberte **použít existující** a pak vyberte **myResourceGroup** . |

    Vytvoření účtu úložiště může trvat kolem minuty. Se zbývajícími kroky nepokračujte, dokud se účet úložiště nevytvoří. Ve všech případech musí být účet úložiště ve stejné oblasti jako NSG.
4. V levé horním rohu portálu vyberte **Všechny služby**. Do **pole Filtr** zadejte *Network Watcher*. Jakmile se služba **Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
5. Pod **PROTOKOLY** vyberte **Protokoly toku NSG**, jak je vidět na tomto obrázku:

    ![Snímek obrazovky zobrazuje protokoly toku Network Watcher NSG.](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. V seznamu NSG vyberte NSG s názvem **myVm-nsg**.
7. V **Nastavení protokolů toku** vyberte **Zapnuto**.
8. Vyberte verzi protokolování toku. Verze 2 obsahuje statistiku relací toků (bajty a pakety).

   ![Vyberte verzi protokolů Flow](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. Vyberte účet úložiště, který jste vytvořili v kroku 3.
   > [!NOTE]
   > Protokoly toku NSG nefungují s účty úložiště, které mají povolený [hierarchický obor názvů](../storage/blobs/data-lake-storage-namespace.md) .
1. V levé horním rohu portálu vyberte **Všechny služby**. Do **pole Filtr** zadejte *Network Watcher*. Jakmile se služba **Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
10. Nastavte **Doba uchování (dny)** na 5 a pak vyberte **Uložit**.

## <a name="download-flow-log"></a>Stažení toku protokolu

1. Ze služby Network Watcher na portálu vyberte **Protokoly toku NSG** pod **PROTOKOLY**.
2. Vyberte **Protokoly toku si můžete stáhnout z nakonfigurovaných účtů úložiště**, jak je vidět na tomto obrázku:

   ![Stažení protokolů toku](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Vyberte účet úložiště, který jste nakonfigurovali ve 2. kroku [Povolení protokolu toku NSG](#enable-nsg-flow-log).
4. V části **BLOB Service** vyberte **kontejnery** a pak vyberte kontejner **Insights-logs-networksecuritygroupflowevent** .
5. V kontejneru přejděte do hierarchie složek, dokud se nedostanete k PT1H.jsv souboru, jak je znázorněno na následujícím obrázku. Soubory protokolu se zapisují do hierarchie složek, které následují po následujících konvencích vytváření názvů: https://{storageAccountName}. blob. Core. Windows. NET/Insights-logs-networksecuritygroupflowevent/resourceId =/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y = {Year}/m = {month}/d = {Day}/h = {Hour}/m = 00/macAddress = {macAddress}/PT1H.jsna

   ![Protokol toku](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. Vyberte **...** vpravo od souboru PT1H.json a vyberte **Stáhnout**.

## <a name="view-flow-log"></a>Zobrazení protokolu toku

Následující kód json je příkladem toho, co se zobrazí v souboru PT1H.json pro každý tok, pro který se protokolují data:

### <a name="version-1-flow-log-event"></a>Událost protokolu toku verze 1
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>Událost protokolu toku verze 2
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

Hodnota **mac** v předchozím výstupu je adresa MAC síťového rozhraní, které se vytvořilo při vytvoření virtuálního počítače. Informace oddělené čárkou pro **flowTuples** vypadají takto:

| Příklad dat | Co data představují   | Vysvětlení                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Časové razítko             | Časové razítko, kdy tok proběhl, ve formátu UNIX EPOCH. V předchozím příkladu se data interpretují jako 1. května 2018 ve 14:59:05 GMT.                                                                                    |
| 10.0.0.4  | Zdrojová IP adresa      | Zdrojová IP adresa, ze které tok pocházel. 10.0.0.4 je privátní IP adresa virtuálního počítače, který jste vytvořili ve [Vytvoření virtuálního počítače](#create-a-vm).
| 13.67.143.118     | Cílová IP adresa | Cílová IP adresa, kam tok mířil.                                                                                  |
| 44931        | Zdrojový port            | Zdrojový port, ze které tok pocházel.                                           |
| 443         | Cílový port       | Cílový port, do kterého tok mířil. Vzhledem k tomu, že provoz byl určen pro port 443, pravidlo s názvem **UserRule_default-Allow-RDP** v souboru protokolu zpracovalo tok.                                                |
| T            | Protokol               | Jestli byl protokol toku TCP (T) nebo UDP (U).                                  |
| O            | Směr              | Jestli byl provoz příchozí (I) nebo odchozí (O).                                     |
| A            | Akce                 | Jestli byl provoz povolený (A) nebo odmítnutý (D).  
| C            | Pouze stav toku **verze 2** | Zachycuje stav toku. Možné stavy jsou při vytváření toku **B**: begin. Statistiky nejsou k dispozici. **C**: pokračuje se na průběžný tok. Statistika je k dispozici v intervalu 5 minut. **E**: end, když se ukončí tok. Statistiky jsou k dispozici. |
| 30 | Odeslané pakety – pouze zdrojová do cílové **verze 2** | Celkový počet paketů TCP nebo UDP odeslaných ze zdroje do cíle od poslední aktualizace. |
| 16978 | Odeslané bajty – pouze zdrojová do cíle **verze 2** | Celkový počet bajtů paketů TCP nebo UDP odeslaných ze zdroje do cíle od poslední aktualizace. Bajty paketů zahrnují hlavičku paketu a datovou část. |
| 24 | Odeslané pakety – pouze cíl až ke zdrojové **verzi 2** | Celkový počet paketů TCP nebo UDP odeslaných z cíle do zdroje od poslední aktualizace. |
| 14008| Odeslané bajty – pouze cíl až ke zdrojové **verzi 2** | Celkový počet bajtů paketů TCP a UDP odeslaných z cíle do zdroje od poslední aktualizace. Bajty paketů zahrnují hlavičku paketu a datovou část.|

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak povolit protokolování toku pro NSG pro NSG. Dál jste zjistili, jak stáhnout a zobrazit data zaprotokolovaná v souboru. Nezpracovaná data v souboru json může být obtížné interpretovat. K vizualizaci dat protokolů toků můžete použít [Azure Analýza provozu](traffic-analytics.md), [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)a další nástroje. Můžete vyzkoušet alternativní metody povolení protokolů toku NSG, jako jsou [PowerShell](network-watcher-nsg-flow-logging-powershell.md), [Azure CLI](network-watcher-nsg-flow-logging-cli.md), [REST API](network-watcher-nsg-flow-logging-rest.md) a [šablony ARM](network-watcher-nsg-flow-logging-azure-resource-manager.md).