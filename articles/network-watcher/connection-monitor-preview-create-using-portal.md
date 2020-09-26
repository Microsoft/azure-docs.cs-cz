---
title: Vytvoření náhledu monitorování připojení – Azure Portal
titleSuffix: Azure Network Watcher
description: Zjistěte, jak pomocí Azure Portal vytvořit monitorování připojení (Preview).
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362901"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Vytvoření monitoru připojení (Preview) pomocí Azure Portal

Naučte se vytvořit monitorování připojení (Preview) a monitorovat komunikaci mezi prostředky pomocí Azure Portal. Podporuje hybridní nasazení cloudu a Azure.

## <a name="before-you-begin"></a>Než začnete 

Ve sledováních připojení, které vytvoříte v monitorování připojení (Preview), můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení (Preview) obsahuje následující entity:


* **Prostředek sledování připojení** – prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod** – zdroj nebo cíl, který se účastní kontrol připojení. Příklady koncových bodů: 
    * Virtuální počítače Azure
    * Virtuální sítě Azure
    * Podsítě Azure
    * Místní agenti
    * Místní podsítě
    * Místní vlastní síť skládající se z několika podsítí
    * Adresy URL a IP adresy 
* **Konfigurace testu** – konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina** – skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test** – kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

    ![Diagram znázorňující monitorování připojení, definování vztahu mezi testovacími skupinami a testy](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Postup vytvoření

Pokud chcete pomocí Azure Portal vytvořit monitorování připojení (Preview), postupujte podle následujících kroků:

1. Na domovské stránce Azure Portal přejít na **Network Watcher**.
1. Na levé straně v části **monitorování** vyberte **monitorování připojení (Preview)**.
1. Zobrazí se všechna monitorování připojení, která byla vytvořena v monitorování připojení (Preview). Chcete-li zobrazit monitory připojení vytvořené v klasickém prostředí monitorování připojení, přejděte na kartu **monitorování připojení** .

    ![Snímek obrazovky zobrazující monitory připojení, které byly vytvořeny v monitorování připojení (Preview)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. Na řídicím panelu **monitorování připojení (Preview)** v levém horním rohu vyberte **vytvořit**.

   ![Snímek obrazovky znázorňující kartu základy v monitorování připojení](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na kartě **základy** zadejte informace pro monitor připojení:
   * **Název sledování připojení** – přidejte název monitorování připojení. Použijte standardní pravidla pojmenování pro prostředky Azure.
   * **Předplatné** – vyberte předplatné pro monitorování připojení.
   * **Oblast** – vyberte oblast pro monitorování připojení. Můžete vybrat jenom zdrojové virtuální počítače, které jsou vytvořené v této oblasti.
   * **Konfigurace pracovního prostoru** – pracovní prostor obsahuje data monitorování. Můžete použít vlastní pracovní prostor nebo výchozí pracovní prostor. 
       * Chcete-li použít výchozí pracovní prostor, zaškrtněte políčko. 
       * Chcete-li zvolit vlastní pracovní prostor, zrušte zaškrtnutí políčka. Pak zvolte předplatné a oblast pro vlastní pracovní prostor. 
1. V dolní části karty vyberte **Další: testovací skupiny**.

   ![Snímek obrazovky s kartou vytvořit testovací skupinu v monitorování připojení](./media/connection-monitor-2-preview/create-tg.png)

1. Přidejte zdroje, cíle a konfigurace testů do testovacích skupin. Chcete-li nastavit testovací skupiny, přečtěte si téma [vytvoření testovacích skupin v monitorování připojení](#create-test-groups-in-a-connection-monitor). 
1. V dolní části karty vyberte **Další: vytvořit výstrahy**.

   ![Snímek obrazovky znázorňující podokno, ve kterém se mají výstrahy vytvářet](./media/connection-monitor-2-preview/create-alert.png)

1. Pokud chcete vytvořit upozornění, přečtěte si téma [vytvoření výstrah v monitorování připojení](#create-alerts-in-connection-monitor) .
1. V dolní části karty vyberte **Další: zkontrolovat + vytvořit**.

  ![Snímek obrazovky s monitorováním připojení, na které se zobrazuje karta Revize + vytvořit](./media/connection-monitor-2-preview/review-create-cm.png)

1. Na kartě **Revize + vytvořit** si přečtěte základní informace a skupiny testů před vytvořením monitorování připojení. Pokud potřebujete upravit monitorování připojení:
   * Chcete-li upravit základní podrobnosti, vyberte ikonu tužky.
   * Chcete-li upravit testovací skupinu, vyberte ji.

   > [!NOTE] 
   > Na kartě **Revize + vytvořit** se zobrazují náklady za měsíc během fáze Preview monitorování připojení. V současné době se ve sloupci **aktuální náklady** zobrazí žádné poplatky. Když je monitorování připojení všeobecně dostupné, v tomto sloupci se zobrazí měsíční poplatek. 
   > 
   > I ve fázi Preview monitorování připojení platí Log Analytics poplatky za ingestování.

1. Až budete připraveni vytvořit monitorování připojení, v dolní části karty **Revize + vytvořit** vyberte **vytvořit**.

Monitorování připojení (Preview) vytvoří prostředek monitorování připojení na pozadí.

## <a name="create-test-groups-in-a-connection-monitor"></a>Vytvoření testovacích skupin v monitorování připojení

Každá testovací skupina v monitorování připojení zahrnuje zdroje a cíle, které se testují na parametrech sítě. Jsou testovány pro procento kontrol, které selžou, a čas od konfigurace testu.

Z Azure Portal pro vytvoření testovací skupiny v monitorování připojení zadáte hodnoty pro následující pole:

* **Zakázat testovací skupinu** – můžete vybrat toto pole, chcete-li zakázat monitorování pro všechny zdroje a cíle, které určuje skupina testů. Ve výchozím nastavení je tento výběr vymazán.
* **Název** – pojmenujte testovací skupinu.
* **Zdroje** – Pokud jsou agenti nainstalováni, můžete zadat jak virtuální počítače Azure, tak i místní počítače jako zdroje. Chcete-li nainstalovat agenta pro zdroj, přečtěte si téma [Instalace agentů monitorování](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Pokud chcete vybrat agenty Azure, vyberte kartu **koncové body Azure** . Tady vidíte jenom virtuální počítače, které jsou svázané s oblastí, kterou jste zadali při vytváření monitorování připojení. Ve výchozím nastavení jsou virtuální počítače seskupené do předplatného, ke kterému patří. Tyto skupiny jsou sbaleny. 
   
       Můžete přejít k podrobnostem z úrovně předplatného na jiné úrovně v hierarchii:

      **Předplatné**  >  **Skupiny prostředků**  >  **Virtuální sítě**  >  **Podsítě**  >  **Virtuální počítače s agenty**

      Můžete také změnit hodnotu pole **Seskupit podle** , aby se strom spouštěl z jiné úrovně. Pokud například vytvoříte seskupení podle virtuální sítě, zobrazí se virtuální počítače, které mají agenty v hierarchii **virtuální sítě**  >  **Subnets**  >  **virtuální počítače podsítí s agenty**.
       Výběr virtuální sítě, podsítě nebo jednoho virtuálního počítače nastaví odpovídající ID prostředku jako koncový bod. Ve výchozím nastavení se v monitorování účastní všechny virtuální počítače ve vybrané virtuální síti nebo podsíti s rozšířením Network Watcher. Chcete-li omezit rozsah, buď vyberte konkrétní podsítě/agenty, nebo změňte hodnotu vlastnosti Scope. 

      ![Snímek obrazovky s monitorováním připojení, který zobrazuje panel Přidat zdroje a kartu agenti Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Pokud chcete vybrat místní agenty, vyberte kartu **agenti, kteří nejsou – Azure** . Ve výchozím nastavení se agenti seskupují do pracovních prostorů podle oblasti. Všechny tyto pracovní prostory mají nakonfigurované řešení Network Performance Monitor. 
   
       Pokud potřebujete přidat Network Performance Monitor do svého pracovního prostoru, Získejte ho od [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Informace o tom, jak přidat Network Performance Monitor, najdete v tématu [monitorování řešení v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       V zobrazení **Sledování připojení** je na kartě **základy** vybraná možnost výchozí oblast. Změníte-li oblast, můžete vybrat možnost agenti z pracovních prostorů v nové oblasti.  Můžete vybrat jednoho nebo více agentů nebo podsítí. V zobrazení podsítě můžete pro monitorování vybrat konkrétní IP adresy. Pokud přidáte více podsítí, vytvoří se vlastní místní síť, konkrétně "OnPremises_Network_1". Můžete také změnit hodnotu pole **Seskupit podle** , aby bylo možné seskupovat podle podsítí.

      ![Snímek obrazovky s monitorováním připojení, zobrazením panelu Přidat zdroje a karty agenti mimo Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * Můžete také zvolit naposledy použité koncové body, pomocí karty **Poslední koncový bod** . 
   
   * Po dokončení nastavení zdrojů vyberte **Hotovo**. Základní vlastnosti, jako je název koncového bodu, můžete i nadále upravovat kliknutím na koncový bod v zobrazení vytvořit skupinu testů. 

* **Cílová umístění** – můžete sledovat připojení k virtuálním počítačům Azure, místním počítačům nebo jakémukoli koncovému bodu (veřejnou IP adresu, URL nebo plně kvalifikovaný název domény), a to tak, že je určíte jako cílová. V jedné skupině testů můžete přidat virtuální počítače Azure, místní počítač, adresy URL pro Office 365, adresy URL Dynamics 365 a vlastní koncové body.

    * Pokud chcete zvolit virtuální počítače Azure jako cíle, vyberte kartu **koncové body Azure** . Ve výchozím nastavení se virtuální počítače Azure seskupují do hierarchie předplatného ve stejné oblasti, kterou jste vybrali v zobrazení **Sledování připojení** na kartě **základy** . Můžete změnit oblast a zvolit virtuální počítače Azure z nově vybrané oblasti. Pak můžete přejít k podrobnostem z úrovně předplatného na jiné úrovně v hierarchii, stejně jako u zdrojových koncových bodů Azure.
     Můžete vybrat virtuální sítě, podsítě nebo samostatné virtuální počítače, podobně jako zdrojový koncový bod Azure. Výběr virtuální sítě, podsítě nebo jednoho virtuálního počítače nastaví odpovídající ID prostředku jako koncový bod. Ve výchozím nastavení se v monitorování účastní všechny virtuální počítače ve vybrané virtuální síti nebo podsíti s rozšířením Network Watcher. Chcete-li omezit rozsah, buď vyberte konkrétní podsítě/agenty, nebo změňte hodnotu vlastnosti Scope. 

       ![Snímek obrazovky s podoknem přidat umístění, na které se zobrazuje karta virtuální počítače Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Snímek obrazovky s podoknem přidat umístění, kde se zobrazuje úroveň předplatného](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Pokud chcete jako cíle zvolit jiné agenty než Azure, vyberte kartu koncových bodů mimo Azure. Ve výchozím nastavení se agenti seskupují do pracovních prostorů podle oblasti. Všechny tyto pracovní prostory mají nakonfigurované řešení Network Performance Monitor. Pokud potřebujete přidat Network Performance Monitor do svého pracovního prostoru, Získejte ho od Azure Marketplace. Informace o tom, jak přidat Network Performance Monitor, najdete v tématu monitorování řešení v Azure Monitor. V zobrazení sledování připojení je na kartě základy vybraná možnost výchozí oblast. Změníte-li oblast, můžete vybrat možnost agenti z pracovních prostorů v nové oblasti. Můžete vybrat jednoho nebo více agentů nebo podsítí. V zobrazení podsítě můžete pro monitorování vybrat konkrétní IP adresy. Pokud přidáte více podsítí, vytvoří se vlastní místní síť, konkrétně "OnPremises_Network_1".  
    
     ![Snímek obrazovky s podoknem přidat cíle mimo Azure](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Pokud chcete jako cíle zvolit veřejné koncové body, vyberte kartu **externí adresy** . Seznam koncových bodů zahrnuje testovací adresy URL Office 365 a adresy URL testů Dynamics 365 seskupené podle názvu. Kromě těchto koncových bodů můžete zvolit koncový bod, který byl vytvořen v jiných testovacích skupinách ve stejném monitorování připojení. 
    
        Chcete-li přidat nový koncový bod, v pravém horním rohu vyberte **+ koncové body**. Pak zadejte název koncového bodu a adresu URL, IP adresu nebo plně kvalifikovaný název domény.

       ![Snímek obrazovky s informacemi o tom, kde přidat veřejné koncové body jako cíle v monitorování připojení](./media/connection-monitor-2-preview/add-endpoints.png)

    * Chcete-li zvolit naposledy použité koncové body, otevřete kartu **nedávné koncové**body   .
    * Po dokončení výběru možnosti cíle vyberte **Hotovo**. Základní vlastnosti, jako je název koncového bodu, můžete i nadále upravovat kliknutím na koncový bod v zobrazení vytvořit skupinu testů. 

* **Konfigurace testů** – ke skupině testů můžete přidružit jednu nebo více konfigurací testu. Vytvořte novou konfiguraci testu pomocí karty nová konfigurace nebo použijte konfiguraci testu použitou v jiných testovacích skupinách v rámci stejného monitorování připojení prostřednictvím karty zvolit existující.

    * **Název** – pojmenujte konfiguraci testu.
    * **Protokol** – vyberte TCP, ICMP nebo http. Pokud chcete změnit protokol HTTP na HTTPS, jako protokol vyberte **http** a jako port vyberte **443** .
        * **Vytvořit konfiguraci testu sítě** – toto zaškrtávací políčko se zobrazí jenom v případě, že jste v poli **protokol** vybrali **http** . Toto políčko zaškrtněte, pokud chcete vytvořit další konfiguraci testu, která používá stejné zdroje a cíle, které jste zadali jinde v konfiguraci. Nově vytvořená konfigurace testu je pojmenována `<the name of your test configuration>_networkTestConfig` .
        * **Disable traceroute** – toto pole se používá, pokud je protokol TCP nebo ICMP. Toto políčko zaškrtněte, pokud chcete zastavit zdroje z vyhledávání topologie a času směrování po směrování.
    * **Cílový port** – toto pole můžete přizpůsobit podle vybraného cílového portu.
        * Naslouchat na portu – toto pole se používá, když je protokol TCP. Toto políčko zaškrtněte, pokud chcete otevřít zvolený port TCP, pokud ještě není otevřený. 
    * **Frekvence testování** – pomocí tohoto pole můžete zvolit, jak často se budou zdroje testovat pomocí příkazu otestovat cílový protokol a port, který jste zadali. Můžete zvolit 30 sekund, 1 minutu, 5 minut, 15 minut nebo 30 minut. Zvolením možnosti vlastní zadáte četnost výběru mezi 30 sekundami a 30 minutami. Zdroje budou testovat připojení k cílům na základě hodnoty, kterou zvolíte.  Pokud například vyberete 30 sekund, zdroje budou kontrolovat připojení k cíli nejméně jednou za 30 sekund.
    * **Prahová hodnota úspěšnosti** – prahové hodnoty můžete nastavit u následujících síťových parametrů:
       * **Kontroly se nezdařily** – nastaví procento kontrol, které mohou selhat, když zdroje kontrolují připojení k cílům pomocí zadaných kritérií. V případě protokolu TCP nebo ICMP může být Procento neúspěšných kontrol rovno procentům ztráty paketů. V případě protokolu HTTP toto pole představuje procento požadavků HTTP, které neobdržely odpověď.
       * **Doba** odezvy – nastavte čas v milisekundách, po který se můžou dlouhé zdroje připojit k cíli přes konfiguraci testu.
    
       ![Snímek obrazovky s informacemi o tom, kde nastavit konfiguraci testu v monitorování připojení](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Vytváření upozornění v monitorování připojení

Můžete nastavit výstrahy na testy, které selžou na základě prahových hodnot nastavených v konfiguracích testu.   

Pokud chcete v monitorování připojení vytvořit upozornění, zadejte v rámci Azure Portal hodnoty následujících polí: 

- Vytvořit výstrahu – toto pole můžete vybrat, chcete-li vytvořit upozornění metriky v Azure Monitor. Výběrem této možnosti povolíte další pole pro úpravy. Další poplatky za výstrahu se použijí na základě [cen pro výstrahy](https://azure.microsoft.com/pricing/details/monitor/) . 

- Rozsah – > prostředky a rozsah – > hierarchie – tato hodnota je předem vyplněna na základě hodnot zadaných na kartě základy. 

- Podmínka – > výstraha se vytvoří v metrikě výsledek testu (Preview). Pokud se nezdařil výsledek testu monitorování připojení, vygeneruje se pravidlo upozornění. 

- Skupina akcí – můžete zvolit přímé zadání e-mailu nebo můžete zvolit vytvoření výstrah prostřednictvím skupin akcí. Pokud se rozhodnete zadat přímo svůj e-mail, vytvoří se skupina akcí se skupinou akcí název NPM E-mail a do této skupiny akcí se přidá ID e-mailu. Pokud se rozhodnete použít skupiny akcí, budete muset vybrat dříve vytvořenou skupinu akcí. Informace o tom, jak vytvořit skupinu akcí, najdete tady. Po úspěšném vytvoření výstrahy můžete pomocí odkazu spravovat výstrahy spravovat výstrahy. 

- Název pravidla výstrahy – název monitorování připojení 

- Při vytváření Povolit pravidlo – povolí pravidlo výstrahy na základě podmínky. Tuto možnost vypněte, pokud chcete vytvořit pravidlo, ale Nepovolit ho. 

    ![Snímek obrazovky znázorňující vytvoření výstrahy v podokně sledování připojení](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>Omezení škálování

Monitory připojení mají následující meze škálování:

* Maximální počet monitorování připojení na předplatné na oblast: 100
* Maximální počet testovacích skupin na monitorování připojení: 20
* Maximální počet zdrojů a cílů na monitorování připojení: 100
* Maximální počet konfigurací testu na monitorování připojení: 2 přes Azure Portal

## <a name="next-steps"></a>Další kroky

* Naučte [se analyzovat data monitorování a nastavovat upozornění](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts) .
* Zjistěte [, jak diagnostikovat problémy ve vaší síti](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network) .
