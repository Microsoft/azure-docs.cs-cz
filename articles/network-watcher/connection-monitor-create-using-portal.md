---
title: Vytvoření náhledu monitorování připojení – Azure Portal
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak vytvořit monitorování ve verzi Preview monitorování připojení pomocí Azure Portal.
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
ms.openlocfilehash: 97d20f2b6b7b355ea5c810ad46b084f42b9bd6d1
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447614"
---
# <a name="create-a-monitor-in-connection-monitor-preview-by-using-the-azure-portal"></a>Vytvoření monitorování ve verzi Preview monitorování připojení pomocí Azure Portal

Naučte se používat Preview monitorování připojení k monitorování komunikace mezi prostředky. Tento článek popisuje, jak vytvořit monitorování pomocí Azure Portal. Monitorování připojení podporuje hybridní a cloudová nasazení Azure.

## <a name="before-you-begin"></a>Než začnete 

V okně Sledování připojení, které vytvoříte pomocí nástroje Connection monitor Preview, můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být v Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Tady jsou některé definice, které vám pomohou začít:

* **Prostředek monitorování připojení** Prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod**. Zdroj nebo cíl, který se účastní kontrol připojení. Mezi příklady koncových bodů patří:
    * Virtuální počítače Azure.
    * Virtuální sítě Azure.
    * Podsítě Azure.
    * Místní agenti.
    * Místní podsítě.
    * Místní vlastní sítě, které obsahují několik podsítí.
    * Adresy URL a IP adresy.
* **Konfigurace testu**. Konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina**. Skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test:** Kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagram znázorňující monitorování připojení a definuje vztah mezi testovacími skupinami a testy.":::


## <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení

Postup vytvoření monitorování ve verzi Preview monitorování připojení pomocí Azure Portal:

1. Na domovské stránce Azure Portal přejít na **Network Watcher**.
1. V levém podokně v části **monitorování** vyberte možnost **monitorování připojení (Preview)**.

   Zobrazí se všechna monitorování připojení, která byla vytvořena ve verzi Preview monitorování připojení. Pokud chcete zobrazit monitory připojení vytvořené v monitorování klasického připojení, přejděte na kartu **monitorování připojení** .

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Snímek obrazovky zobrazující monitory připojení vytvořené v nástroji Connection monitor Preview":::
   
    
1. V řídicím panelu **monitorování připojení (Preview)** v levém horním rohu vyberte **vytvořit**.

   

1. Na kartě **základy** zadejte informace pro monitor připojení: 
   * **Název monitorování připojení** : zadejte název monitoru připojení. Použijte standardní pravidla pojmenování pro prostředky Azure.
   * **Předplatné** : vyberte předplatné pro monitorování připojení.
   * **Oblast** : Vyberte oblast pro monitorování připojení. Můžete vybrat jenom zdrojové virtuální počítače, které jsou vytvořené v této oblasti.
   * **Konfigurace pracovního prostoru** : vyberte vlastní pracovní prostor nebo výchozí pracovní prostor. Váš pracovní prostor obsahuje data monitorování.
       * Chcete-li použít výchozí pracovní prostor, zaškrtněte políčko. 
       * Chcete-li zvolit vlastní pracovní prostor, zrušte zaškrtnutí políčka. Pak vyberte předplatné a oblast pro vlastní pracovní prostor. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Snímek obrazovky zobrazující kartu základy v monitorování připojení":::
   
1. V dolní části karty vyberte **Další: testovací skupiny**.

1. Přidejte zdroje, cíle a konfigurace testů do testovacích skupin. Další informace o nastavení testovacích skupin naleznete v tématu [Create test groups in Connection monitor](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Snímek obrazovky zobrazující kartu skupiny testů v monitorování připojení":::

1. V dolní části karty vyberte **Další: vytvořit výstrahy**. Další informace o vytváření výstrah najdete v tématu o vytváření [výstrah v monitorování připojení](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Snímek obrazovky, který zobrazuje kartu vytvořit výstrahu.":::

1. V dolní části karty vyberte **Další: zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** si přečtěte základní informace a skupiny testů před vytvořením monitorování připojení. Pokud potřebujete upravit monitorování připojení, můžete to udělat tak, že přejdete zpátky na příslušné karty. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Snímek obrazovky zobrazující kartu Revize + vytvořit v monitorování připojení":::
   > [!NOTE] 
   > Na kartě **Revize + vytvořit** se zobrazují náklady za měsíc během fáze Preview monitorování připojení. V současné době se ve sloupci **aktuální náklady/měsíc** zobrazuje bez poplatků. Když je monitorování připojení všeobecně dostupné, v tomto sloupci se zobrazí měsíční poplatek. 
   > 
   > I během fáze Preview monitorování připojení platí Log Analytics poplatky za ingestování.

1. Až budete připraveni vytvořit monitorování připojení, v dolní části karty **Revize + vytvořit** vyberte **vytvořit**.

Connection monitor Preview vytvoří prostředek monitorování připojení na pozadí.

## <a name="create-test-groups-in-a-connection-monitor"></a>Vytvoření testovacích skupin v monitorování připojení

Každá testovací skupina v monitorování připojení zahrnuje zdroje a cíle, které se testují na parametrech sítě. Jsou testovány pro procento kontrol, které selžou, a čas od konfigurace testu.

V Azure Portal pro vytvoření testovací skupiny v monitorování připojení zadáte hodnoty pro následující pole:

* **Zakázat testovací skupinu** : Toto políčko můžete zaškrtnout, pokud chcete zakázat monitorování pro všechny zdroje a cíle, které určuje skupina testů. Ve výchozím nastavení je tento výběr vymazán.
* **Název** : pojmenujte testovací skupinu.
* **Zdroje** : Pokud jsou agenti nainstalováni, můžete zadat jak virtuální počítače Azure, tak i místní počítače jako zdroje. Informace o instalaci agenta pro zdroj najdete v tématu [Instalace agentů monitorování](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Pokud chcete vybrat agenty Azure, vyberte kartu **koncové body Azure** . Tady vidíte jenom virtuální počítače, které jsou svázané s oblastí, kterou jste zadali při vytváření monitorování připojení. Ve výchozím nastavení jsou virtuální počítače seskupené do předplatného, ke kterému patří. Tyto skupiny jsou sbaleny. 
   
       Můžete přejít k podrobnostem z úrovně **předplatného** na jiné úrovně v hierarchii:

      **Předplatné**  >  **Skupina prostředků**  >  **Virtuální síť**  >  **Podsíť**  >  **Virtuální počítače s agenty**

      Můžete také změnit selektor **Group by** , aby se strom spouštěl z jiné úrovně. Pokud například vytvoříte seskupení podle virtuální sítě, zobrazí se virtuální počítače, které mají agenty v hierarchii **VNET**  >  virtuálních počítačů **podsítě** virtuální sítě  >  **s agenty**.

       Když vyberete virtuální síť, podsíť nebo jeden virtuální počítač, odpovídající ID prostředku se nastaví jako koncový bod. Ve výchozím nastavení se v monitorování účastní všechny virtuální počítače ve vybrané virtuální síti nebo podsíti, které mají rozšíření Azure Network Watcher. Chcete-li omezit rozsah, buď vyberte konkrétní podsítě nebo agenty, nebo změňte hodnotu vlastnosti Scope. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Snímek obrazovky, který zobrazuje podokno přidat zdroje a kartu koncových bodů Azure v monitorování připojení.":::

   * Pokud chcete vybrat místní agenty, vyberte kartu **koncové body mimo Azure** . Ve výchozím nastavení se agenti seskupují do pracovních prostorů podle oblasti. U všech těchto pracovních prostorů je Network Performance Monitor nakonfigurovaný. 
   
       Pokud potřebujete přidat Network Performance Monitor do svého pracovního prostoru, Získejte ho od [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Informace o tom, jak přidat Network Performance Monitor, najdete v tématu [monitorování řešení v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       V části **vytvořit monitorování připojení** je na kartě **základy** vybraná možnost výchozí oblast. Změníte-li oblast, můžete vybrat možnost agenti z pracovních prostorů v nové oblasti. Můžete vybrat jednoho nebo více agentů nebo podsítí. V zobrazení **podsítě** můžete pro monitorování vybrat konkrétní IP adresy. Pokud přidáte více podsítí, vytvoří se vlastní místní síť s názvem **OnPremises_Network_1** . V selektoru **skupiny** můžete také změnit seskupení podle agentů.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Snímek obrazovky, který zobrazuje podokno přidat zdroje a kartu koncových bodů mimo Azure v monitorování připojení.":::

   * Pokud chcete zvolit naposledy použité koncové body, můžete použít kartu **Poslední koncový bod** . 
   
   * Po dokončení nastavení zdrojů vyberte **Hotovo** v dolní části karty. Základní vlastnosti, jako je název koncového bodu, můžete i nadále upravovat tak, že v zobrazení **vytvořit skupinu testů** vyberete koncový bod. 

* **Cílová umístění** : můžete monitorovat připojení k virtuálnímu počítači Azure, místnímu počítači nebo libovolnému koncovému bodu (veřejné IP adresy, adrese URL nebo plně kvalifikovanému názvu domény), a to tak, že ho zadáte jako cíl. V jedné skupině testů můžete přidat virtuální počítače Azure, místní počítače, adresy URL pro Office 365, adresy URL Dynamics 365 a vlastní koncové body.

    * Pokud chcete zvolit virtuální počítače Azure jako cíle, vyberte kartu **koncové body Azure** . Ve výchozím nastavení se virtuální počítače Azure seskupují do hierarchie předplatného, která je v oblasti vybrané v části **vytvořit monitorování připojení** na kartě **základy** . Můžete změnit oblast a zvolit virtuální počítače Azure z nové oblasti. Pak můžete přejít k podrobnostem z úrovně **předplatného** na jiné úrovně v hierarchii, stejně jako při nastavování zdrojových koncových bodů Azure.

      Můžete vybrat virtuální sítě, podsítě nebo jeden virtuální počítač, jak můžete nastavit zdrojové koncové body Azure. Když vyberete virtuální síť, podsíť nebo jeden virtuální počítač, odpovídající ID prostředku se nastaví jako koncový bod. Ve výchozím nastavení se monitorování účastní všechny virtuální počítače ve vybrané virtuální síti nebo podsíti, které mají rozšíření Network Watcher. Chcete-li omezit rozsah, buď vyberte konkrétní podsítě nebo agenty, nebo změňte hodnotu vlastnosti Scope. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<snímek obrazovky, který zobrazuje podokno přidat umístění a kartu koncových bodů Azure >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<snímek obrazovky, který zobrazuje podokno přidat cíle na úrovni předplatného. >":::
       
    
    * Pokud chcete jako cíle vybrat jiné agenty než Azure, vyberte kartu **koncových bodů mimo Azure** . Ve výchozím nastavení se agenti seskupují do pracovních prostorů podle oblasti. Všechny tyto pracovní prostory jsou Network Performance Monitor nakonfigurované. 
    
      Pokud potřebujete přidat Network Performance Monitor do svého pracovního prostoru, Získejte ho od Azure Marketplace. Informace o tom, jak přidat Network Performance Monitor, najdete v tématu [monitorování řešení v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 

      V části **vytvořit monitorování připojení** je na kartě **základy**   Vybraná možnost výchozí oblast. Změníte-li oblast, můžete vybrat možnost agenti z pracovních prostorů v nové oblasti. Můžete vybrat jednoho nebo více agentů nebo podsítí. V zobrazení **podsítě** můžete pro monitorování vybrat konkrétní IP adresy. Pokud přidáte více podsítí, vytvoří se vlastní místní síť s názvem **OnPremises_Network_1** .  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Snímek obrazovky, který zobrazuje podokno přidat cíle a kartu koncových bodů mimo Azure.":::
    
    * Pokud chcete jako cíle zvolit veřejné koncové body, vyberte kartu **externí adresy** . Seznam koncových bodů zahrnuje testovací adresy URL Office 365 a adresy URL testů Dynamics 365 seskupené podle názvu. Můžete také zvolit koncové body, které byly vytvořeny v jiných testovacích skupinách ve stejném monitorování připojení. 
    
        Pokud chcete přidat koncový bod, v pravém horním rohu vyberte **přidat koncový bod**. Pak zadejte název koncového bodu a adresu URL, IP adresu nebo plně kvalifikovaný název domény.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Snímek obrazovky, který ukazuje, kde přidat veřejné koncové body jako cíle v monitorování připojení.":::

    * Pokud chcete zvolit naposledy použité koncové body, otevřete na kartě **Poslední koncový bod**   .
    * Po dokončení výběru možnosti cíle vyberte **Hotovo**. Základní vlastnosti, jako je název koncového bodu, můžete i nadále upravovat tak, že v zobrazení **vytvořit skupinu testů** vyberete koncový bod. 

* **Konfigurace testů** : můžete přidat jednu nebo více konfigurací testu do testovací skupiny. Vytvořte novou konfiguraci testu pomocí karty **Nová konfigurace** . Nebo přidejte konfiguraci testu z jiné testovací skupiny v rámci stejného monitorování připojení z karty **Vybrat existující** .

    * **Název konfigurace testu** : název konfigurace testu.
    * **Protokol** : vyberte **TCP** , **ICMP** nebo **http**. Pokud chcete změnit protokol HTTP na HTTPS, jako protokol vyberte **http** a pak jako port vyberte **443** .
        * **Vytvořit konfiguraci testu TCP** : Toto zaškrtávací políčko se zobrazí jenom v případě, že jste v seznamu **protokol** vybrali **http** . Toto políčko zaškrtněte, pokud chcete vytvořit další konfiguraci testu, která bude používat stejné zdroje a cíle, které jste zadali jinde v konfiguraci. Nová konfigurace testu má název **\<name of test configuration> _networkTestConfig**.
        * **Zakázat traceroute** : Toto zaškrtávací políčko se použije, pokud je protokol TCP nebo ICMP. Toto políčko zaškrtněte, pokud chcete zastavit zdroje z vyhledávání topologie a času směrování po směrování.
    * **Cílový port** : můžete zadat cílový port, který si zvolíte.
        * **Naslouchat na portu** : Toto zaškrtávací políčko se použije, když je protokol TCP. Zaškrtnutím tohoto políčka otevřete vybraný port TCP, pokud ještě není otevřený. 
    * **Frekvence testování** : v tomto seznamu určete, jak často se budou zdroje testovat na základě protokolu a portu, které jste zadali. Můžete zvolit 30 sekund, 1 minutu, 5 minut, 15 minut nebo 30 minut. Vyberte možnost **vlastní** a zadejte jinou frekvenci mezi 30 sekundami a 30 minutami. Zdroje budou testovat připojení k cílům na základě hodnoty, kterou zvolíte. Pokud například vyberete 30 sekund, zdroje budou kontrolovat připojení k cílovému umístění nejméně jednou za každých 30 sekund.
    * **Prahová hodnota úspěšnosti** : můžete nastavit prahové hodnoty u těchto síťových parametrů:
       * **Kontroly se nezdařily** : nastavte procento kontrol, které mohou selhat, když zdroje kontrolují připojení k cílům pomocí zadaných kritérií. V případě protokolu TCP nebo ICMP může být Procento neúspěšných kontrol rovno procentům ztráty paketů. V případě protokolu HTTP tato hodnota představuje procento požadavků HTTP, které neobdržely odpověď.
       * **Doba** odezvy: nastavte čas RTT (v milisekundách), jak dlouho můžou zdroje trvat pro připojení k cíli prostřednictvím konfigurace testu.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Snímek obrazovky, který ukazuje, kde nastavit konfiguraci testu v monitorování připojení":::
       
## <a name="create-alerts-in-connection-monitor"></a>Vytváření upozornění v monitorování připojení

Můžete nastavit výstrahy na testy, které selžou na základě prahových hodnot nastavených v konfiguracích testu.

V Azure Portal chcete vytvořit výstrahy pro monitorování připojení, zadejte hodnoty pro tato pole: 

- **Vytvořit výstrahu** : Toto políčko můžete zaškrtnout, pokud chcete vytvořit upozornění metriky v Azure monitor. Když zaškrtnete toto políčko, ostatní pole budou povolena pro úpravy. Další poplatky za výstrahu se použijí na základě [cen pro výstrahy](https://azure.microsoft.com/pricing/details/monitor/). 

- **Rozsah**  >  **Prostředek**  >  **Hierarchie** : tyto hodnoty jsou automaticky vyplněny na základě hodnot zadaných na kartě **základy** .

- **Název podmínky** : výstraha se vytvoří u `Test Result(preview)` metriky. Pokud výsledek testu monitorování připojení selže, pravidlo výstrahy se aktivuje. 

- **Název skupiny akcí** : můžete zadat přímo svůj e-mail nebo můžete vytvořit upozornění prostřednictvím skupin akcí. Pokud přímo zadáte svůj e-mail, vytvoří se skupina akcí, která má vytvořenou skupinu akcí s názvem **npm e-mail** . ID e-mailu se přidá do této skupiny akcí. Pokud se rozhodnete použít skupiny akcí, je nutné vybrat dříve vytvořenou skupinu akcí. Informace o tom, jak vytvořit skupinu akcí, najdete v tématu [Vytvoření skupin akcí v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). Po vytvoření výstrahy můžete [Spravovat výstrahy](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#view-and-manage-with-azure-portal). 

- **Název pravidla výstrahy** : název monitorování připojení.

- **Povolit pravidlo při vytváření** : Zaškrtnutím tohoto políčka povolíte pravidlo výstrahy na základě podmínky. Toto políčko zakažte, pokud chcete vytvořit pravidlo bez jeho povolení. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Snímek obrazovky, který zobrazuje kartu vytvořit výstrahu v monitorování připojení.":::

## <a name="scale-limits"></a>Omezení škálování

Monitory připojení mají tyto meze škálování:

* Maximální počet monitorování připojení na předplatné na oblast: 100
* Maximální počet testovacích skupin na monitorování připojení: 20
* Maximální počet zdrojů a cílů na monitorování připojení: 100
* Maximální počet konfigurací testu na monitorování připojení: 2 přes Azure Portal

## <a name="next-steps"></a>Další kroky

* Naučte [se analyzovat data monitorování a nastavovat upozornění](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts).
* Naučte [se diagnostikovat problémy ve vaší síti](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network).
