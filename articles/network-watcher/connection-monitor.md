---
title: Monitorování síťové komunikace – kurz – Azure Portal | Microsoft Docs
description: Naučte se monitorovat síťovou komunikaci mezi dvěma virtuálními počítači pomocí funkce monitorování připojení v Azure Network Watcheru.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 0c865b8bc129f4f2809f2dbb09a836efe4cee3d9
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093036"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Kurz: Monitorování síťové komunikace mezi dvěma virtuálními počítači na webu Azure Portal

Úspěšná komunikace mezi virtuálním počítačem a koncovým bodem, jako je například jiný virtuální počítač, může být pro organizaci velmi důležitá. Někdy se zavádějí změny konfigurace, po kterých může dojít k narušení komunikace. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit dva virtuální počítače
> * Monitorovat komunikaci mezi virtuálními počítači pomocí funkce monitorování připojení v Network Watcheru
> * Generovat upozornění na metriky monitorování připojení
> * Diagnostikovat a vyřešit potíže s komunikací mezi dvěma virtuálními počítači

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-vms"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a pak vyberte operační systém. V tomto kurzu se používá **Windows Server 2016 Datacenter**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm1|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** vyberte **Rozšíření**. Vyberte **Přidat rozšíření** a vyberte **Network Watcher Agent for Windows**, jak ukazuje následující obrázek:

    ![Rozšíření Agent Network Watcher](./media/connection-monitor/nw-agent-extension.png)

6. V části **Network Watcher Agent for Windows** vyberte **Vytvořit**, v části **Instalovat rozšíření** vyberte **OK** a pak v části **Rozšíření** vyberte **OK**.
7. Potvrďte výchozí hodnoty pro ostatní **nastavení** a vyberte **OK**.
8. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Proveďte znovu kroky uvedené v části [Vytvoření prvního virtuálního počítače](#create-the-first-vm), ale s následujícími změnami:

|Krok|Nastavení|Hodnota|
|---|---|---|
| 1 | Vyberte **Ubuntu Server 17.10 VM**. |                                                                         |
| 3 | Název                              | myVm2                                                                   |
| 3 | Typ ověřování               | Vložte váš veřejný klíč SSH nebo vyberte **Heslo** a zadejte heslo. |
| 3 | Skupina prostředků                    | Vyberte **Použít existující** a pak vyberte **myResourceGroup**.                 |
| 6 | Rozšíření                        | **Network Agent for Linux**                                             |

Nasazení virtuálního počítače trvá několik minut. Než budete pokračovat ve zbývajících krocích, počkejte, až virtuální počítač dokončí nasazování.

## <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení

Vytvořte monitorování připojení pro monitorování komunikace přes port TCP 22 z *myVm1* do *myVm2*.

1. Na levé straně portálu vyberte **Všechny služby**.
2. Do pole **Filtr** začněte psát *network watcher*. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
3. V části **MONITOROVÁNÍ** vyberte **Monitorování připojení**.
4. Vyberte **+ Přidat**.
5. Zadejte nebo vyberte informace o připojení, které chcete monitorovat, a pak vyberte **Přidat**. V příkladu na následujícím obrázku se monitoruje připojení z virtuálního počítače *myVm1* do virtuálního počítače *myVm2* přes port 22:

    | Nastavení                  | Hodnota               |
    | ---------                | ---------           |
    | Název                     | myVm1-myVm2(22)     |
    | Zdroj                   |                     |
    | Virtuální počítač          | myVm1               |
    | Cíl              |                     |
    | Vyberte virtuální počítač |                     |
    | Virtuální počítač          | myVm2               |
    | Port                     | 22                  |

    ![Přidat monitorování připojení](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Zobrazení monitorování připojení

1. Pomocí kroků 1–3 v části [Vytvoření monitorování připojení](#create-a-connection-monitor) zobrazte monitorování připojení. Zobrazí se seznam existujících monitorování připojení jako na následujícím obrázku:

    ![Monitorování připojení](./media/connection-monitor/connection-monitors.png)

2. Vyberte monitorování s názvem **myVm1-myVm2(22)**, jak je znázorněno na předchozím obrázku, abyste zobrazili podrobnosti pro toto monitorování, jak je znázorněno na následujícím obrázku:

    ![Podrobnosti monitorování](./media/connection-monitor/vm-monitor.png)

    Všimněte si následujících informací:

    | Položka                     | Hodnota                      | Podrobnosti                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Dostupné                  | Oznamuje, jestli je koncový bod dostupný nebo ne.|
    | PRŮM. DOBA ODEZVY          | Oznamuje dobu odezvy pro vytvoření připojení v milisekundách. Monitorování připojení testuje připojení každých 60 sekund, takže můžete monitorovat latenci v čase.                                         |
    | Směrování                     | Monitorování připojení oznamuje segmenty směrování mezi dvěma koncovými body. V tomto příkladu existuje připojení mezi dvěma virtuálními počítači ve stejné virtuální síti, takže je tam jenom jeden segment směrování, a to na IP adresu 10.0.0.5. Pokud provoz mezi virtuálními počítači směruje nějaký existující systém nebo vlastní směrování, například přes bránu VPN nebo síťové virtuální zařízení, budou uvedené další segmenty směrování.                                                                                                                         |
    | STAV                   | Zelené značky zaškrtnutí u jednotlivých koncových bodů oznamují, že dané koncové body jsou v pořádku.    ||

## <a name="generate-alerts"></a>Generování upozornění

Upozornění vytvářejí pravidla upozornění služby Azure Monitor. Pravidla mohou v pravidelných intervalech automaticky spouštět uložené dotazy nebo vlastní prohledávání protokolů. Vygenerované upozornění může automaticky spustit jednu nebo více akcí, například odeslat někomu oznámení nebo spustit jiný proces. Při nastavování pravidla upozornění se seznam dostupných metrik, které můžete ke generování upozornění použít, určí podle prostředku, na který cílíte.

1. Na webu Azure Portal vyberte službu **Monitor** a pak vyberte **Upozornění** > **Nové pravidlo upozornění**.
2. Klikněte na **Vybrat cíl** a pak vyberte prostředky, na které chcete cílit. Vyberte **Předplatné** a nastavte **Typ prostředku**, abyste vyfiltrovali monitorování připojení, které chcete použít.

    ![Obrazovka upozornění s vybraným cílem](./media/connection-monitor/set-alert-rule.png)
1. Jakmile vyberete prostředek, na který chcete cílit, vyberte **Přidat kritéria**. Network Watcher nabízí [metriky, podle kterých můžete vytvářet upozornění](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#metrics-and-dimensions-supported). Nastavte **Dostupné signály** na metriky ProbesFailedPercent a AverageRoundtripMs:

    ![Stránka upozornění s vybranými signály](./media/connection-monitor/set-alert-signals.png)
1. Vyplňte podrobnosti o upozornění, jako je název pravidla upozornění, popis a závažnost. K upozornění můžete také přidat skupinu akcí, která vám umožní automatizovat a přizpůsobit reakci na upozornění.

## <a name="view-a-problem"></a>Zobrazení problému

Azure ve výchozím nastavení umožňuje komunikaci mezi virtuálními počítači ve stejné virtuální síti přes všechny porty. V průběhu času je možné, že někdo ve vaší organizaci výchozí pravidla Azure přepíše a tím nechtěně způsobí chybu komunikace. Provedením následujících kroků vytvořte problém s komunikací a pak znovu zobrazte monitorování připojení:

1. Do pole Hledat v horní části portálu zadejte *myResourceGroup*. Když se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte skupinu zabezpečení sítě **myVm2-nsg**.
3. Vyberte **Příchozí pravidla zabezpečení** a pak vyberte **Přidat**, jak je znázorněno na následujícím obrázku:

    ![Příchozí pravidla zabezpečení](./media/connection-monitor/inbound-security-rules.png)

4. Výchozím pravidlem, které umožňuje komunikaci mezi všemi virtuálními počítači ve virtuální síti, je pravidlo s názvem **AllowVnetInBound**. Vytvořte pravidlo s vyšší prioritou (nižším číslem) než pravidlo **AllowVnetInBound**, které bude odepírat příchozí komunikaci přes port 22. Vyberte nebo zadejte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Přidat**:

    | Nastavení                 | Hodnota          |
    | ---                     | ---            |
    | Rozsahy cílových portů | 22             |
    | Akce                  | Odepřít           |
    | Priorita                | 100            |
    | Název                    | DenySshInbound |

5. Monitorování připojení provádí testování v intervalech 60 sekund, a proto počkejte několik minut a pak na levé straně portálu vyberte **Network Watcher**, pak **Monitorování připojení** a pak znovu vyberte monitorování **myVm1-myVm2(22)**. Výsledky se teď liší, jak můžete vidět na následujícím obrázku:

    ![Chyba monitorování podrobností](./media/connection-monitor/vm-monitor-fault.png)

    Ve sloupci se stavem pro síťové rozhraní **myvm2529** se zobrazuje červený vykřičník.

6. Pokud chcete zjistit, proč se tento stav změnil, vyberte na předchozím obrázku položku 10.0.0.5. Monitorování připojení vám oznámí příčinu chyby komunikace: *Provoz se zablokoval kvůli následujícímu pravidlu skupiny zabezpečení sítě: UserRule_DenySshInbound*.

    Pokud jste nevěděli, že někdo implementoval pravidlo zabezpečení, které jste vytvořili v kroku 4, zjistili byste z monitorování připojení, že příčinou problému s komunikací je toto pravidlo. Komunikaci mezi virtuálními počítači můžete potom obnovit tak, že dané pravidlo změníte, přepíšete nebo odeberete.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili monitorovat připojení mezi dvěma virtuálními počítači. Zjistili jste, že pravidlo skupiny zabezpečení sítě zabránilo komunikaci s virtuálním počítačem. Pokud chcete získat informace o všech různých odpovědích, které monitorování připojení může vrátit, podívejte se na [typy odpovědí](network-watcher-connectivity-overview.md#response). Můžete monitorovat také připojení mezi virtuálním počítačem, plně kvalifikovaným názvem domény, identifikátorem URI nebo IP adresou.

V určitém okamžiku můžete zjistit, že prostředky ve virtuální síti nemůžou komunikovat s prostředky v jiných sítích, které jsou připojené pomocí brány virtuální sítě Azure. Přejděte k dalšímu kurzu, ve kterém se dozvíte, jak diagnostikovat potíže s bránou virtuální sítě.

> [!div class="nextstepaction"]
> [Diagnostika potíží s komunikací mezi sítěmi](diagnose-communication-problem-between-networks.md)
