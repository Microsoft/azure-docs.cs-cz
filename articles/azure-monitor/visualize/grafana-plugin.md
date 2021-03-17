---
title: Monitorování služeb a aplikací Azure pomocí Grafana
description: Směrovat Azure Monitor a Application Insights data, abyste je mohli zobrazit v Grafana.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: decd0353bcb2cf048fd5591ea8c895cf22944809
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048654"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorování služeb Azure v Grafana
Služby a aplikace Azure teď můžete monitorovat z [Grafana](https://grafana.com/) pomocí [modulu plug-in zdroje dat Azure monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Modul plug-in shromažďuje data o výkonu aplikací shromážděná Azure Monitor, včetně různých protokolů a metrik. Tato data pak můžete zobrazit na řídicím panelu Grafana.

Pomocí následujících kroků můžete nastavit server Grafana a vytvářet řídicí panely pro metriky a protokoly z Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Nastavení serveru Grafana

### <a name="set-up-grafana-locally"></a>Nastavení Grafana místně
Pokud chcete nastavit místní server Grafana, [Stáhněte a nainstalujte Grafana do svého místního prostředí](https://grafana.com/grafana/download). Pokud chcete použít integraci Azure Monitor modulu plug-in, nainstalujte Grafana verze 5,3 nebo vyšší.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Nastavení Grafana v Azure pomocí Azure Marketplace
1. Přejít na Azure Marketplace a vybrat Grafana podle Grafana Labs.

2. Zadejte názvy a podrobnosti. Vytvoříte novou skupinu prostředků. Sledujte hodnoty, které zvolíte pro uživatelské jméno virtuálního počítače, heslo virtuálního počítače a heslo správce serveru Grafana.  

3. Vyberte velikost virtuálního počítače a účet úložiště.

4. Nakonfigurujte nastavení konfigurace sítě.

5. Zobrazte souhrn a po přijetí podmínek použití vyberte **vytvořit** .

6. Po dokončení nasazení vyberte **Přejít do skupiny prostředků**. Zobrazí se seznam nově vytvořených prostředků.

    ![Objekty skupiny prostředků Grafana](media/grafana-plugin/grafana1.png)

    Pokud vyberete skupinu zabezpečení sítě (*grafana-NSG* v tomto případě), uvidíte, že se k přístupu k serveru grafana používá port 3000.

7. Získejte veřejnou IP adresu vašeho serveru Grafana – vraťte se do seznamu prostředků a vyberte **Veřejná IP adresa**.

## <a name="sign-in-to-grafana"></a>Přihlásit se k Grafana

1. Pomocí IP adresy vašeho serveru otevřete přihlašovací stránku na adrese *http:// \<IP address\> : 3000* nebo *\<DNSName> \: 3000* v prohlížeči. I když je 3000 výchozí port, Všimněte si, že při instalaci jste vybrali jiný port. Měla by se zobrazit přihlašovací stránka pro server Grafana, který jste vytvořili.

    ![Přihlašovací obrazovka Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Přihlaste se pomocí *účtu správce* uživatelských jmen a hesla správce serveru Grafana, které jste vytvořili dříve. Pokud používáte místní instalaci, bude výchozí heslo *správce* a vy budete požádáni o jeho změnu při prvním přihlášení.

## <a name="configure-data-source-plugin"></a>Konfigurace modulu plug-in zdroje dat

Po úspěšném přihlášení byste měli vidět, že modul plug-in zdroje dat Azure Monitor je už zahrnutý.

![Grafana zahrnuje Azure Monitor modul plug-in](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Pokud chcete přidat a nakonfigurovat Azure Monitor zdroj dat, vyberte **Přidat zdroj dat** .

2. Vyberte název zdroje dat a jako typ z rozevíracího seznamu vyberte **Azure monitor** .

3. Vytvoření instančního objektu – služba Grafana používá instanční objekt Azure Active Directory pro připojení k rozhraním API Azure Monitor a shromažďování dat. Abyste mohli spravovat přístup k prostředkům Azure, musíte vytvořit nebo použít existující instanční objekt.
    * K vytvoření instančního objektu se podívejte na [tyto pokyny](../../active-directory/develop/howto-create-service-principal-portal.md) . Zkopírujte a uložte ID tenanta (ID adresáře), ID klienta (ID aplikace) a tajný klíč klienta (hodnota klíče aplikace).
    * Pokud chcete přiřadit roli Čtenář k aplikaci Azure Active Directory v předplatném, skupině prostředků nebo prostředku, který chcete monitorovat, přečtěte si téma [přiřazení aplikace k roli](../../active-directory/develop/howto-create-service-principal-portal.md) . 
    Rozhraní Log Analytics API vyžaduje [roli čtecího modulu Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-reader), která zahrnuje oprávnění role čtenáře a přidává je k němu.

4. Zadejte podrobnosti o připojení k rozhraním API, která chcete použít. Můžete se připojit k některým z nich nebo k některým z nich. 
    * Pokud se k oběma metrikám a protokolům přihlašujete v Azure Monitor, můžete stejné přihlašovací údaje znovu použít tak, že vyberete **stejné podrobnosti jako Azure monitor API**.
    * Při konfiguraci modulu plug-in můžete určit, který cloud Azure byste chtěli monitorovat (veřejné, Azure USA, Azure Německo nebo Azure Čína).
    * Pokud používáte Application Insights, můžete také zahrnout rozhraní API Application Insights a ID aplikace, abyste mohli shromažďovat metriky založené na Application Insights. Další informace najdete v tématu [získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Některá pole zdroje dat jsou pojmenována jinak než jejich korelační nastavení Azure:
        > * ID tenanta je ID adresáře Azure.
        > * ID klienta je Azure Active Directory ID aplikace.
        > * Tajný kód klienta je hodnota Azure Active Directoryho klíče aplikace.

5. Pokud používáte Application Insights, můžete také zahrnout rozhraní API Application Insights a ID aplikace, abyste mohli shromažďovat metriky založené na Application Insights. Další informace najdete v tématu [získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Vyberte **Uložit** a Grafana bude testovat přihlašovací údaje pro každé rozhraní API. Měla by se zobrazit zpráva podobná následující.  
    ![Konfigurace zdroje dat Grafana byla schválena](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Vytvoření řídicího panelu Grafana

1. Přejít na domovskou stránku Grafana a vyberte **nový řídicí panel**.

2. Na novém řídicím panelu vyberte **graf**. Můžete vyzkoušet další možnosti vytváření grafů, ale v tomto článku se jako příklad používá *graf* .

3. Na řídicím panelu se zobrazí prázdný graf. Klikněte na název panelu a vyberte **Upravit** a zadejte podrobnosti o datech, která chcete vykreslit do tohoto grafu grafu.
    ![Grafana nový graf](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Vyberte Azure Monitor zdroj dat, který jste nakonfigurovali.
   * Shromažďují se metriky Azure Monitor – v rozevíracím seznamu služba vyberte **Azure monitor** . Zobrazí se seznam selektorů, kde můžete vybrat prostředky a metriku, která se má monitorovat v tomto grafu. Pokud chcete shromáždit metriky z virtuálního počítače, použijte obor názvů **Microsoft. COMPUTE/VirtualMachines**. Po výběru virtuálních počítačů a metrik můžete začít zobrazovat jejich data na řídicím panelu.
     ![Konfigurace grafu Grafana pro Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Shromažďují se data protokolu Azure Monitor – v rozevíracím seznamu služba vyberte **Azure Log Analytics** . Vyberte pracovní prostor, ve kterém se chcete dotazovat, a nastavte text dotazu. Můžete zkopírovat libovolný dotaz protokolu, který už máte, nebo vytvořit nový. Při psaní v dotazu se IntelliSense zobrazí a navrhne možnosti automatického dokončování. Vyberte typ vizualizace, **tabulku** **časových řad** a spusťte dotaz.
    
     > [!NOTE]
     >
     > Výchozí dotaz dodaný s modulem plug-in používá dvě makra: "$ __timeFilter () a $ __interval. 
     > Tato makra umožňují Grafana dynamicky vypočítat časový rozsah a časový interval při přiblížení části grafu. Tato makra můžete odebrat a použít standardní časový filtr, jako je například *TimeGenerated > před (1H)*, což znamená, že graf nepodporuje funkci přiblížení.
    
     ![Konfigurace grafu Grafana pro Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Následuje jednoduchý řídicí panel se dvěma grafy. Na levé straně se zobrazuje procento využití procesoru u dvou virtuálních počítačů. Graf na pravé straně zobrazuje transakce v účtu Azure Storage členěné podle typu rozhraní API pro transakce.
    ![Příklad Grafana dvou grafů](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Volitelné: Sledujte vlastní metriky na stejném serveru Grafana

Můžete také nainstalovat telegraf a InfluxDB ke shromáždění a vykreslení stejné instance Grafana instance a jejich využití. K dispozici je mnoho datových modulů plug-in, které můžete použít k spojování těchto metrik na řídicím panelu.

Můžete také znovu použít toto nastavení, abyste zahrnuli metriky ze serveru Prometheus. Použijte modul plug-in Prometheus data source v galerii modulů plug-in Grafana.

Tady jsou dobré referenční články, jak používat telegraf, InfluxDB, Prometheus a Docker.
 - [Jak monitorovat systémové metriky se zásobníkem značek na Ubuntu 16,04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Řešení monitorování pro hostitele Docker, kontejnery a kontejnerové služby](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Tady je obrázek celého řídicího panelu Grafana, který obsahuje metriky z Azure Monitor a Application Insights.
![Grafana ukázkové metriky](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Pokročilé funkce Grafana

### <a name="variables"></a>Proměnné
Některé hodnoty dotazu je možné vybrat prostřednictvím rozevíracích seznamů uživatelského rozhraní a aktualizovat v dotazu. Jako příklad Vezměte v úvahu následující dotaz:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Můžete nakonfigurovat proměnnou, která bude zobrazovat všechny dostupné hodnoty **řešení** , a pak aktualizovat dotaz tak, aby se použil.
Pokud chcete vytvořit novou proměnnou, klikněte na tlačítko Nastavení řídicího panelu v pravém horním rohu, vyberte **proměnné** a pak **Nový**.
Na stránce proměnná Definujte zdroj dat a dotaz, který se má spustit, aby se získal seznam hodnot.
![Grafana konfigurovat proměnnou](./media/grafana-plugin/grafana-configure-variable-dark.png)

Po vytvoření Upravte dotaz tak, aby používal vybrané hodnoty a že vaše grafy budou odpovídajícím způsobem reagovat:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana použít proměnné](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Vytváření seznamů skladeb řídicích panelů

Jednou z mnoha užitečných funkcí Grafana je seznam skladeb řídicího panelu. Můžete vytvořit několik řídicích panelů a přidat je do seznamu stop Konfigurace intervalu pro zobrazení jednotlivých řídicích panelů. Výběrem **Přehrát** zobrazíte cyklus řídicích panelů. Je možné, že je budete chtít zobrazit na velkém monitoru a poskytnout stavovou desku pro vaši skupinu.

![Příklad seznamu testů Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste nastavili prostředí Grafana v Azure, budou se vám účtovat poplatky za spuštění virtuálních počítačů bez ohledu na to, jestli je používáte, nebo ne. Abyste se vyhnuli dalším poplatkům, vyčistěte skupinu prostředků vytvořenou v tomto článku.

1. V nabídce na levé straně Azure Portal klikněte na **skupiny prostředků** a pak klikněte na **Grafana**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **Grafana** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky
* [Přehled metrik Azure Monitor](../data-platform.md)
