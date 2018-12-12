---
title: Místní SQL Server – aplikace Azure Machine Learning Studio | Dokumentace Microsoftu
description: Data z místní databáze systému SQL Server použijte k provádění pokročilých analýz Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.openlocfilehash: 059dd3d902409abfa80be2b7aac579a54de1868f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090666"
---
# <a name="perform-analytics-with-azure-machine-learning-using-an-on-premises-sql-server-database"></a>Provádění analýz pomocí Azure Machine Learning pomocí k místní databázi SQL serveru

Často podniky, které fungují s místními daty by chtěli využívat škálování a flexibility cloudu k jejich počítači strojové učení. Ale nebudete chtít narušit jejich aktuální obchodní procesy a pracovní postupy díky přesunu jejich místní data do cloudu. Azure Machine Learning teď podporuje čtení dat z místní databáze systému SQL Server a pak trénování a vyhodnocování modelu s těmito daty. Už máte ručně zkopírovat a synchronizovat data mezi cloudem a místním serverem. Místo toho **Import dat** modulu v nástroji Azure Machine Learning Studio teď najdete přímo z místní databáze SQL serveru pro trénování a vyhodnocování úlohy.

Tento článek poskytuje přehled o tom, jak příchozího přenosu dat místní data SQL serveru do Azure Machine Learning Studio. Předpokládá, že jste obeznámeni s koncepty Azure Machine Learning, jako jsou pracovní prostory, moduly, datové sady, experimenty, *atd*.

> [!NOTE]
> Tato funkce není k dispozici pro bezplatné pracovní prostory. Další informace o úrovních a Machine Learning – ceny najdete v tématu [Azure Machine Learning – ceny](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalace prostředí Integration Runtime Data Factory
Pro přístup k místní databázi SQL serveru ve službě Azure Machine Learning, budete muset stáhnout a nainstalovat Data Factory IR, dřív označované jako brána pro správu dat. Při konfiguraci připojení v nástroji Machine Learning Studio, máte možnost stáhnout a nainstalovat ji pomocí Integration Runtime (IR) **stahování a brána dat registru** dialogového okna popsané níže.


Můžete si také nainstalovat prostředí IR předem stažením a instalací balíčku MSI Instalační program z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI lze použít také k upgradu existujících IR na nejnovější verzi, se všemi možnými nastavení zachovány.

Data Factory modul Integration Runtime má následující požadavky:

* Integrace místním objekt pro vytváření dat vyžaduje 64-bit operační systém pomocí rozhraní .NET Framework 4.6.1 nebo vyšší.
* Podporované verze operačního systému Windows jsou Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Doporučená konfigurace pro počítač reakcí na Incidenty, je nejméně 2 GHz, 4 jádra procesoru, 8GB paměti RAM a 80GB disk.
* Pokud hostitelský počítač přejde do režimu spánku, prostředí IR neodpovídá na požadavky na data. Proto konfigurovat schéma napájení příslušné v v počítači před instalací IR. Pokud je počítač nakonfigurovaný do režimu hibernace, instalace prostředí IR zobrazí zprávu.
* Vzhledem k tomu, že konkrétní frekvencí dojde k aktivitě kopírování, využití prostředků (procesor, paměť) na počítači také používá stejný vzor s ve špičce a doby nečinnosti. Využití prostředků také závisí do značné míry na množství dat, který se přesouvá. Když probíhají úlohy s více kopií, budete sledovat využití prostředků zvýší během špiček. Minimální konfigurace uvedené výše je technicky dostatečná, můžete chtít mít konfigurací s více prostředků, než minimální požadavky na konfiguraci v závislosti na konkrétní zatížení pro přesun dat.

Vezměte v úvahu následující při nastavování a pomocí Data Factory modul Integration Runtime:

* V jednom počítači můžete nainstalovat jenom jednu instanci reakcí na Incidenty.
* Jednotné prostředí IR můžete použít pro více zdrojů dat v místním prostředí.
* Více IRs na různé počítače můžete připojit ke stejnému zdroji dat místní.
* Nakonfigurujte IRs pro jenom jeden pracovní prostor v čase. V současné době IRs se nedají sdílet mezi pracovními prostory.
* Můžete nakonfigurovat více IRs pro jeden pracovní prostor. Můžete například použít prostředí IR, který je připojený ke zdrojům dat testu během vývoje a provozu reakcí na Incidenty až budete připraveni na zprovoznění.
* Prostředí IR nemusí být ve stejném počítači jako zdroj dat. Ale zůstává blíž ke zdroji dat snižuje čas potřebný pro bránu pro připojení ke zdroji dat. Doporučujeme nainstalovat prostředí IR v počítači, který je jiný než ten, který je hostitelem místního zdroje dat tak, aby brána a zdroj dat není soutěží o prostředky.
* Pokud už máte IR nainstalovaný ve vašem počítači obsluhující scénářů Power BI nebo služby Azure Data Factory, nainstalujte samostatné prostředí IR pro Azure Machine Learning v jiném počítači.

  > [!NOTE]
  > Data Factory modul Integration Runtime a Power BI Gateway nelze spustit ve stejném počítači.
  >
  >
* Budete muset použít Data Factory modul Integration Runtime pro Azure Machine Learning, i když používáte Azure ExpressRoute pro další data. Zdroje dat by měly zpracovávat jako zdroj dat v místním (který je za bránou firewall) i při použití ExpressRoute. K navázání připojení mezi Machine Learning a zdroji dat pomocí Data Factory modul Integration Runtime.

Podrobné informace o požadavky na instalaci, instalační kroky a tipy pro řešení potíží najdete v článku [prostředí Integration Runtime ve službě Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Příchozí přenos dat z místní databáze SQL serveru do Azure Machine Learning
V tomto podrobném návodu nastavit modul Runtime integrace Azure Data Factory v pracovním prostoru Azure Machine Learning, nakonfigurujte ji a pak si můžete přečíst data z databáze SQL serveru v místním.

> [!TIP]
> Než začnete, zakažte v prohlížeči blokování automaticky otevíraných oken pro `studio.azureml.net`. Pokud používáte prohlížeč Google Chrome, stáhnout a nainstalovat některou z několika moduly plug-in k dispozici v Google Chrome WebStore [klikněte jednou rozšíření aplikace](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Brána správy dat se dřív používal Azure Data Factory modul Integration Runtime. Podrobný kurz nadále označujeme jako bránu.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Vytvoření brány
Prvním krokem je vytvoření a nastavení brány pro přístup k vaší místní databáze SQL.

1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/Home/) a vyberte pracovní prostor, který chcete pracovat.
2. Klikněte na tlačítko **nastavení** okna na levé straně a pak klikněte na tlačítko **brány DATA GATEWAYS** kartě v horní části.
3. Klikněte na tlačítko **novou bránu DATA GATEWAY** v dolní části obrazovky.

    ![Novou bránu Data Gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. V **novou bránu data gateway** dialogové okno, zadejte **název brány** a volitelně přidejte **popis**. Klikněte na šipku v pravém dolním rohu přejdete k dalšímu kroku konfigurace.

    ![Zadejte název brány a popis](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. V stahování a zaregistrujte data gateway dialogovém okně zkopírujte do schránky brány registrační klíč.

    ![Stáhněte si a registraci brány dat](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Pokud dosud jste stáhli a nainstalovaná Brána pro správu dat společnosti Microsoft, klepněte na tlačítko **brána správy dat stažení**. Tím přejdete na webu Microsoft Download Center kde můžete vybrat verze brány, které potřebujete, si ho stáhnout a nainstalovat ho. Podrobné informace o požadavky na instalaci, instalační kroky a tipy pro řešení potíží najdete v části začátku tohoto článku [přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat](../../data-factory/tutorial-hybrid-copy-portal.md) .
7. Po instalaci brány se otevře Data Management Gateway Configuration Manager a **registrace brány** se zobrazí dialogové okno. Vložit **brány registrační klíč** , který jste zkopírovali do schránky a klikněte na tlačítko **zaregistrovat**.
8. Pokud už máte nainstalovanou bránu, spusťte Správce konfigurace brány správy dat. Klikněte na tlačítko **změnit klíč**, vložte **brány registrační klíč** , který jste zkopírovali do schránky. v předchozím kroku a klikněte na **OK**.
9. Po dokončení instalace **registrace brány** se zobrazí dialogové okno pro Microsoft Data Management Gateway Configuration Manager. Vložte brány registrační klíč, který jste zkopírovali do schránky. v předchozím kroku a klikněte na tlačítko **zaregistrovat**.

    ![Registrace brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Když tyto hodnoty jsou nastavené na dokončení konfigurace brány **Domů** kartu v Microsoft Data Management Gateway Configuration Manageru:

    * **Název brány** a **název Instance** jsou nastaveny na název brány.
    * **Registrace** je nastavena na **registrované**.
    * **Stav** je nastavena na **spuštěno**.
    * Stavového řádku v dolní části zobrazí **připojená ke cloudové službě Data Management Gateway** spolu s zelená značka zaškrtnutí.

      ![Správce brány správy dat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio také aktualizuje po úspěšné registraci.

    ![Registrace brány byla úspěšná](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. V **stáhnout a registrovat bránu data gateway** dialogového okna, klikněte na zaškrtávací políčko k dokončení instalace. **Nastavení** stránce se zobrazuje stav brány jako "Online". V pravém podokně zjistíte, stav a další užitečné informace.

    ![Nastavení brány](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. V Microsoft Data Management Gateway Configuration Manageru přejděte **certifikát** kartu. Certifikát uvedený na této kartě se používá k šifrování/dešifrování přihlašovacích údajů pro místní úložiště dat, které jste zadali v portálu. Tento certifikát je výchozí certifikát. Společnost Microsoft doporučuje změna na svůj vlastní certifikát, který je zálohovat v systému pro správu certifikátů. Klikněte na tlačítko **změnu** místo toho použít vlastní certifikát.

    ![Změnit certifikát brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (volitelné) Pokud chcete povolit podrobné protokolování, abyste mohli řešit problémy s bránou v Microsoft Data Management Gateway Configuration Manageru přejděte **diagnostiky** kartě a zaškrtněte **povolit podrobné protokolování pro účely odstraňování potíží** možnost. Informace o protokolování najdete v prohlížeči událostí Windows v části **protokoly aplikací a služeb**  - &gt; **brána správy dat** uzlu. Můžete také použít **diagnostiky** kartu a otestujte připojení k místním zdroji dat pomocí brány.

    ![Zapnout podrobné protokolování](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Tím dokončíte proces instalace brány ve službě Azure Machine Learning.
Nyní jste připraveni používat vaše místní data.

Můžete vytvořit a nastavit více bran v nástroji Studio za každý pracovní prostor. Například může mít bránu, kterou chcete připojit ke zdrojům dat testu během vývoje a jinou bránu pro zdroje dat produkčního prostředí. Azure Machine Learning poskytuje flexibilitu pro nastavení více bran v závislosti na vašem podnikovém prostředí. Momentálně nemůžete sdílet brány mezi pracovními prostory a jenom jedna brána lze nainstalovat v jednom počítači. Další informace najdete v tématu [přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Použití brány číst data z místních zdrojů dat
Jakmile nastavíte bránu, můžete přidat **Import dat** modulů na experiment, který vstupů data z místní databáze SQL serveru.

1. V nástroji Machine Learning Studio, vyberte **EXPERIMENTY** klikněte na tlačítko **+ nová** v levém dolním rohu a vyberte **prázdný Experiment** (nebo vyberte některou z několika vzorku experimentů k dispozici).
2. Najděte a přetáhněte **Import dat** modulů na plátno experimentu.
3. Klikněte na tlačítko **uložit jako** dole na plátně. Zadejte "Azure kurz strojového učení v místním SQL Server" pro název experimentu, vyberte pracovní prostor a klikněte na tlačítko **OK** zaškrtávací políčko.

   ![Uložit experimentu s novým názvem.](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klikněte na tlačítko **Import dat** modul se vybere, pak v **vlastnosti** v podokně napravo od plátna, vyberte "On-Premises SQL Database" **zdroj dat** rozevíracího seznamu.
5. Vyberte **bránu Data gateway** instalaci a registraci. Jinou bránu můžete nastavit tak, že vyberete "(Přidat novou bránu Data Gateway...)".

   ![Vyberte bránu dat pro modulu Import dat](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Zadejte SQL **název databázového serveru** a **název_databáze**, spolu s SQL **databázový dotaz** chcete spustit.
7. Klikněte na tlačítko **zadejte hodnoty** pod **uživatelské jméno a heslo** a zadejte svoje přihlašovací údaje databáze. Můžete použít integrované ověřování Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.

   ![Zadejte přihlašovací údaje databáze](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Změny "hodnoty požadované" zpráva "sada hodnot" s zelená značka zaškrtnutí. Stačí zadat přihlašovací údaje jednou, pokud se změní informace o databázi nebo heslo. Azure Machine Learning používá certifikát, který jste zadali při instalaci brány pro šifrování přihlašovacích údajů v cloudu. Azure nikdy ukládá místních přihlašovacích údajů bez šifrování.

   ![Import modulu vlastnosti dat](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klikněte na tlačítko **spustit** ke spuštění testu.

Po dokončení testu můžete vizualizovat data importovat z databáze klikněte na výstupní port modulu **Import dat** modulu a vyberete **vizualizovat**.

Jakmile dokončíte, vývoji experimentů, můžete nasazení a zprovoznění vašeho modelu. Služba Batch Execution, data z místního SQL serveru pomocí databáze nakonfigurovaná v **Import dat** modulu čtení, který se používá pro vyhodnocení. Zatímco odpovědi služby žádosti můžete použít pro vyhodnocování místním datům, společnost Microsoft doporučuje používat [Excelový doplněk](excel-add-in-for-web-services.md) místo. V současné době zápisu do místní databáze systému SQL Server prostřednictvím **exportovat Data** není podporováno buď ve své experimenty nebo publikované webové služby.
