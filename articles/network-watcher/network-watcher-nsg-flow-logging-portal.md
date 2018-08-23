---
title: Protokolování toku síťového provozu do a z virtuálního počítače – kurz – Azure Portal | Microsoft Docs
description: Zjistěte, jak protokolovat tok síťového provozu do a z virtuálního počítače pomocí funkce protokolů toku NSG služby Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: f010bebcf1130b3061c60987ffbd4e706a030773
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "41918623"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Kurz: Protokolování síťového provozu do a z virtuálního počítače pomocí portálu Azure Portal

Skupina zabezpečení sítě (NSG) umožňuje filtrovat příchozí provoz do a odchozí provoz z virtuálního počítače. Můžete protokolovat síťový provoz procházející skupinou zabezpečení sítě pomocí funkce protokolu toku NSG služby Network Watcher. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač se skupinou zabezpečení sítě
> * Povolit Network Watcher a zaregistrovat poskytovatele Microsoft.Insights
> * Povolit protokol toku provozu pro NSG pomocí funkce protokolu toku NSG služby Network Watcher
> * Stáhnout data protokolu
> * Zobrazit data protokolu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a pak vyberte **Windows Server 2016 Datacenter** nebo **Ubuntu Server 17.10 VM**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** přijměte všechny výchozí hodnoty a vyberte **OK**.
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače. Nasazení virtuálního počítače trvá několik minut. Než budete pokračovat ve zbývajících krocích, počkejte, až virtuální počítač dokončí nasazování.

Vytvoření virtuálního počítače trvá několik minut. Se zbývajícími kroky nepokračujte, dokud se vytvoření virtuálního počítače nedokončí. Když portál vytvoří virtuální počítač, vytvoří také skupinu zabezpečení sítě s názvem **myVM-nsg** a přidruží ji k síťovému rozhraní virtuálního počítače.

## <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud už máte sledovací proces sítě v oblasti USA – východ povolený, přejděte na [Registrace poskytovatele Insights](#register-insights-provider).

1. Na webu Azure Portal vyberte **Všechny služby**. Do **pole filtru** zadejte *Network Watcher*. Jakmile se služba **Network Watcher** zobrazí ve výsledcích, vyberte ji.
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
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**.

    | Nastavení        | Hodnota                                                        |
    | ---            | ---   |
    | Název           | Délka 3–24 znaků, může obsahovat jenom malá písmena a čísla a musí být jedinečný v rámci všech účtů Azure Storage.                                                               |
    | Umístění       | Vyberte **USA – východ**.                                           |
    | Skupina prostředků | Vyberte **Použít existující** a pak vyberte **myResourceGroup**. |

    Vytvoření účtu úložiště může trvat kolem minuty. Se zbývajícími kroky nepokračujte, dokud se účet úložiště nevytvoří. Pokud místo vytvoření nového účtu úložiště používáte už existující účet, vyberte účet úložiště, který má vybrané **Všechny sítě** (výchozí) v možnosti **Brány firewall a virtuální sítě** v **NASTAVENÍ** pro účet úložiště.
4. V levé horním rohu portálu vyberte **Všechny služby**. Do **pole Filtr** zadejte *Network Watcher*. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
5. Pod **PROTOKOLY** vyberte **Protokoly toku NSG**, jak je vidět na tomto obrázku:

    ![Skupiny NSG](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. V seznamu NSG vyberte NSG s názvem **myVm-nsg**.
7. V **Nastavení protokolů toku** vyberte **Zapnuto**.
8. Vyberte účet úložiště, který jste vytvořili v kroku 3.
9. Nastavte **Doba uchování (dny)** na 5 a pak vyberte **Uložit**.

## <a name="download-flow-log"></a>Stažení toku protokolu

1. Ze služby Network Watcher na portálu vyberte **Protokoly toku NSG** pod **PROTOKOLY**.
2. Vyberte **Protokoly toku si můžete stáhnout z nakonfigurovaných účtů úložiště**, jak je vidět na tomto obrázku:

  ![Stažení protokolů toku](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Vyberte účet úložiště, který jste nakonfigurovali ve 2. kroku [Povolení protokolu toku NSG](#enable-nsg-flow-log).
4. Vyberte **Kontejnery** pod **BLOB SERVICE** a pak vyberte kontejner **insights-logs-networksecuritygroupflowevent**, jak je vidět na tomto obrázku:

    ![Výběr kontejneru](./media/network-watcher-nsg-flow-logging-portal/select-container.png)
5. Projděte hierarchií složek, dokud se nedostanete na soubor PT1H.json, jak je vidět na tomto obrázku:

    ![Soubor protokolu](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

    Soubory protokolu se zapisují do hierarchie složek, která se řídí následující konvencí názvů: https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

6. Vyberte **...**  vpravo od souboru PT1H.json a vyberte **Stáhnout**.

## <a name="view-flow-log"></a>Zobrazení protokolu toku

Následující kód json je příkladem toho, co se zobrazí v souboru PT1H.json pro každý tok, pro který se protokolují data:

```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [{
            "rule": "UserRule_default-allow-rdp",
            "flows": [{
                "mac": "000D3A170C69",
                "flowTuples": ["1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"]
            }]
        }]
    }
}
```

Hodnota **mac** v předchozím výstupu je adresa MAC síťového rozhraní, které se vytvořilo při vytvoření virtuálního počítače. Informace oddělené čárkou pro **flowTuples** vypadají takto:

| Příklad dat | Co data představují   | Vysvětlení                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1525186745   | Časové razítko             | Časové razítko, kdy tok proběhl, ve formátu UNIX EPOCH. V předchozím příkladu se data interpretují jako 1. května 2018 ve 14:59:05 GMT.                                                                                    |
| 192.168.1.4  | Zdrojová IP adresa      | Zdrojová IP adresa, ze které tok pocházel.
| 10.0.0.4     | Cílová IP adresa | Cílová IP adresa, kam tok mířil. 10.0.0.4 je privátní IP adresa virtuálního počítače, který jste vytvořili ve [Vytvoření virtuálního počítače](#create-a-vm).                                                                                 |
| 55960        | Zdrojový port            | Zdrojový port, ze které tok pocházel.                                           |
| 3389         | Cílový port       | Cílový port, do kterého tok mířil. Vzhledem k tomu, že tok mířil do portu 3389, pravidlo s názvem **UserRule_default-allow-rdp** v souboru protokolu tok zpracovalo.                                                |
| T            | Protocol (Protokol)               | Jestli byl protokol toku TCP (T) nebo UDP (U).                                  |
| I            | Směr              | Jestli byl provoz příchozí (I) nebo odchozí (O).                                     |
| A            | Akce                 | Jestli byl provoz povolený (A) nebo odmítnutý (D).                                           |

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak povolit protokolování toku pro NSG pro NSG. Dál jste zjistili, jak stáhnout a zobrazit data zaprotokolovaná v souboru. Nezpracovaná data v souboru json může být obtížné interpretovat. K vizualizaci dat můžete použít [analýzu provozu](traffic-analytics.md) Network Watcher, Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) a další nástroje.