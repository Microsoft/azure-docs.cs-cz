---
title: Místní SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Pomocí dat z místní databáze SQL Serveru můžete provádět pokročilou analýzu pomocí Azure Machine Learning Studio (klasické).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204178"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Provádění analýz pomocí Azure Machine Learning Studio (klasické) pomocí místní databáze SQL Serveru

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Často podniky, které pracují s místními daty by chtěli využít škálování a pružnost cloudu pro své úlohy strojového učení. Ale nechtějí narušit své aktuální obchodní procesy a pracovní postupy přesunutím místních dat do cloudu. Azure Machine Learning Studio (classic) teď podporuje čtení dat z místní databáze SQL Serveru a potom trénování a vyhodnocování modelu s těmito daty. Už nemusíte ručně kopírovat a synchronizovat data mezi cloudem a místním serverem. Místo toho modul **Import dat** v Azure Machine Learning Studio (klasické) teď můžete číst přímo z vaší místní databáze SQL Serveru pro vaše úlohy školení a vyhodnocování.

Tento článek poskytuje přehled o tom, jak příchozí místní sql server data do Azure Machine Learning Studio (klasické). Předpokládá, že jste obeznámeni s koncepty studia (klasické), jako jsou pracovní prostory, moduly, datové sady, experimenty *atd.*

> [!NOTE]
> Tato funkce není k dispozici pro volné pracovní prostory. Další informace o cenách machine learningu a úrovních najdete v [tématu Azure Machine Learning Pricing](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalace prostředí Runtime vlastní integrace datové továrny
Chcete-li získat přístup k místní databázi SERVERU SQL Server v Azure Machine Learning Studio (klasické), je třeba stáhnout a nainstalovat modul Runtime integrace vlastní hostované datové továrny, dříve známý jako brána pro správu dat. Při konfiguraci připojení v Machine Learning Studio (klasické), máte možnost stáhnout a nainstalovat integrační runtime (IR) pomocí **stáhnout a zaregistrovat dialogové okno brány dat** popsané níže.


Infračervený přenos můžete také nainstalovat předem stažením a spuštěním instalačního balíčku MSI ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=39717). MSI lze také použít k upgradu existující infračerveného počítače na nejnovější verzi, se všemi nastaveními zachována.

Prostředí Integrace s vlastní hostovcí datové továrny má následující předpoklady:

* Integrace s vlastním hostitelem datové továrny vyžaduje 64bitový operační systém s rozhraním .NET Framework 4.6.1 nebo vyšším.
* Podporované verze operačního systému Windows jsou Windows 10 , Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Doporučená konfigurace infračerveného počítače je alespoň 2 GHz, 4 Core CPU, 8GB RAM a 80GB disk.
* Pokud hostitelský počítač přejde do režimu spánku, infračervený přenos nebude reagovat na požadavky na data. Proto před instalací infračerveného systému nakonfigurujte příslušné plánnapájení v počítači. Pokud je zařízení nakonfigurováno pro hibernaci, infračervená instalace zobrazí zprávu.
* Vzhledem k tomu, že aktivita kopírování probíhá na určité frekvenci, využití prostředků (procesor, paměť) v počítači také sleduje stejný vzor s špičkou a nečinnosti. Využití prostředků také závisí do značné míry na množství dat, které jsou přesunuty. Pokud probíhá více úloh kopírování, budete sledovat, jak se využití prostředků v době špičky zvýšila. Zatímco minimální konfigurace uvedená výše je technicky dostatečná, můžete chtít mít konfiguraci s více prostředky než minimální konfigurace v závislosti na konkrétní zatížení pro přesun dat.

Při nastavování a používání prostředí Runtime integrace s vlastním hostitelem datové továrny zvažte následující:

* Do jednoho počítače můžete nainstalovat pouze jednu instanci infračerveného infračerveného infračerveného přenosu.
* Můžete použít jeden infračervený přenos pro více místních zdrojů dat.
* Ke stejnému místnímu zdroji dat můžete připojit více ir v různých počítačích.
* Můžete nakonfigurovat iRs pouze pro jeden pracovní prostor najednou. V současné době nelze sdílet iRs mezi pracovními prostory.
* Můžete nakonfigurovat více irs pro jeden pracovní prostor. Můžete například chtít použít infračervený přenos, který je připojen k testovacím zdrojům dat během vývoje, a produkční infračervený přenos, když jste připraveni k provozu.
* Infračervený přenos nemusí být ve stejném počítači jako zdroj dat. Ale zůstat blíže ke zdroji dat snižuje dobu pro bránu pro připojení ke zdroji dat. Doporučujeme nainstalovat infračervený přenos na počítač, který se liší od počítače, který hostuje místní zdroj dat, aby brána a zdroj dat nesoutěžily o prostředky.
* Pokud už máte v počítači nainstalovanou infračervený přenos, která obsluhuje scénáře Power BI nebo Azure Data Factory, nainstalujte do jiného počítače samostatný infračervený přenos pro Azure Machine Learning Studio (klasický).

  > [!NOTE]
  > Na stejném počítači nelze spustit vlastní hostované integrační runtime datové továrny a bránu Power BI.
  >
  >
* Musíte použít data Factory vlastní hostované integrace Runtime pro Azure Machine Learning Studio (klasické), i když používáte Azure ExpressRoute pro jiná data. Zdroj dat byste měli považovat za místní zdroj dat (který je za bránou firewall), i když používáte ExpressRoute. Pomocí prostředí S vlastním hostitelem datové továrny můžete vytvořit připojení mezi Machine Learningem a zdrojem dat.

Podrobné informace o požadavcích na instalaci, postupech instalace a tipy pro řešení potíží najdete v článku [Integration Runtime in Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Příchozí přenos dat z místní databáze SQL Serveru do Azure Machine Learning
V tomto návodu nastavíte modul Runtime integrace Azure Data Factory v pracovním prostoru Azure Machine Learning, nakonfigurujete ho a pak načtete data z místní databáze SQL Serveru.

> [!TIP]
> Než začnete, zakažte blokování automaticky `studio.azureml.net`otevíraných míst prohlížeče pro aplikaci . Pokud používáte prohlížeč Google Chrome, stáhněte a nainstalujte jeden z několika zásuvných modulů dostupných na webu Google Chrome WebStore [Click Once App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory vlastní hostované integrace Runtime byl dříve známý jako brána pro správu dat. Kurz krok za krokem bude nadále odkazovat na to jako brána.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Vytvoření brány
Prvním krokem je vytvoření a nastavení brány pro přístup k místní databázi SQL.

1. Přihlaste se do [Azure Machine Learning Studio (klasické)](https://studio.azureml.net/Home/) a vyberte pracovní prostor, ve kterém chcete pracovat.
2. Klikněte na okno **NASTAVENÍ** vlevo a potom klikněte na kartu **DATOVÉ BRÁNY** nahoře.
3. V dolní části obrazovky klikněte na **NOVÁ DATOVÁ BRÁNA.**

    ![Nová brána dat](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. V dialogovém okně **Nová brána dat** zadejte **název brány** a volitelně přidejte **popis**. Kliknutím na šipku v pravém dolním rohu přejdete k dalšímu kroku konfigurace.

    ![Zadejte název a popis brány.](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. V dialogovém okně Stáhnout a zaregistrovat bránu dat zkopírujte do schránky registrační klíč BRÁNY.

    ![Stažení a registrace brány dat](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Pokud jste ještě nestáhli a nenainstalovali bránu Microsoft Data Management Gateway, klepněte na tlačítko **Stáhnout bránu pro správu dat**. Tím přejdete na službu Stažení softwaru, kde můžete vybrat verzi brány, kterou potřebujete, stáhnout ji a nainstalovat. Podrobné informace o požadavcích na instalaci, postupech instalace a tipy pro řešení potíží najdete v počátečních částech článku [Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Po instalaci brány se otevře Správce konfigurace brány správy dat a zobrazí se dialogové okno **Brány registrace.** Vložte **registrační klíč brány,** který jste zkopírovali do schránky, a klepněte na **tlačítko Registrovat**.
8. Pokud již máte nainstalovanou bránu, spusťte Správce konfigurace brány pro správu dat. Klepněte na **změnit klíč**, vložte **registrační klíč brány,** který jste zkopírovali do schránky v předchozím kroku, a klepněte na **OK**.
9. Po dokončení instalace se zobrazí dialogové okno **Brána registrace** pro nástroj Microsoft Data Management Gateway Configuration Manager. Vložte registrační klíč brány, který jste zkopírovali do schránky v předchozím kroku, a klepněte na **tlačítko Registrovat**.

    ![Registrace brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfigurace brány je dokončena, když jsou na kartě **Domů** ve Správci konfigurace brány microsoft pro správu dat nastaveny následující hodnoty:

    * **Název brány** a **název instance** jsou nastaveny na název brány.
    * **Registrace** je **nastavena**na Registrováno .
    * **Stav** je nastaven na **Spuštěno**.
    * Stavový řádek v dolní části zobrazuje **službu Connected to Data Management Gateway Cloud Service** spolu se zeleným zaškrtnutím.

      ![Správce brány pro správu dat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klasické) se také aktualizuje, když je registrace úspěšná.

    ![Registrace brány byla úspěšná.](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. V dialogovém okně **Stáhnout a zaregistrovat bránu dat** dokončete nastavení kliknutím na zaškrtnutí. Na stránce **Nastavení** se zobrazí stav brány jako "Online". V pravém podokně najdete stav a další užitečné informace.

    ![Nastavení brány](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. V nástroji Microsoft Data Management Gateway Configuration Manager přepněte na kartu **Certifikát.** Certifikát zadaný na této kartě se používá k šifrování nebo dešifrování pověření pro místní úložiště dat, které zadáte na portálu. Tento certifikát je výchozím certifikátem. Společnost Microsoft doporučuje změnit tento certifikát na vlastní certifikát, který zálohujete v systému správy certifikátů. Chcete-li místo toho použít vlastní certifikát, klepněte na **tlačítko Změnit.**

    ![Změna certifikátu brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (nepovinné) Pokud chcete povolit podrobné protokolování za účelem řešení problémů s bránou, v Microsoft Data Management Gateway Configuration Manager přepnout na kartu **Diagnostika** a **zkontrolujte možnost Povolit podrobné protokolování pro účely řešení potíží.** Informace o protokolování naleznete v Prohlížeči událostí systému Windows pod uzětem Brána **pro správu dat** **protokoly**  - &gt; aplikací a služeb. Kartu **Diagnostika** můžete také použít k testování připojení k místnímu zdroji dat pomocí brány.

    ![Zapnout podrobné protokolování](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Tím se dokončí proces nastavení brány v Azure Machine Learning Studio (klasické).
Teď jste připraveni k použití místních dat.

Můžete vytvořit a nastavit více bran v aplikaci Studio (klasické) pro každý pracovní prostor. Můžete mít například bránu, kterou chcete během vývoje připojit k testovacím zdrojům dat, a jinou bránu pro produkční zdroje dat. Azure Machine Learning Studio (klasické) vám poskytuje flexibilitu při nastavovat více bran v závislosti na podnikovém prostředí. V současné době nelze sdílet bránu mezi pracovními prostory a v jednom počítači lze nainstalovat pouze jednu bránu. Další informace najdete v tématu [Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Použití brány ke čtení dat z místního zdroje dat
Po nastavení brány můžete přidat modul **importu dat** do experimentu, který zadává data z místní databáze serveru SQL Server.

1. V Machine Learning Studiu (klasika) vyberte kartu **EXPERIMENTY,** v levém dolním rohu klikněte na **+NOVÝ** a vyberte **Prázdný experiment** (nebo vyberte jeden z několika dostupných ukázkových experimentů).
2. Najděte a přetáhněte modul **Importovat data** na plátno experimentu.
3. Pod plátnem klikněte na **Uložit jako.** Zadejte "Azure Machine Learning Studio (klasické) On-Local SQL Server Tutorial" pro název experimentu, vyberte pracovní prostor a klikněte na značku **OK** zaškrtnutí.

   ![Uložit experiment s novým názvem](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klikněte na modul **Importovat data** a vyberte ho v podokně **Vlastnosti** napravo od plátna a v rozevíracím seznamu **Zdroj dat** vyberte "Místní databáze SQL".
5. Vyberte **bránu dat,** kterou jste nainstalovali a zaregistrovali. Můžete nastavit jinou bránu výběrem "(přidat novou bránu dat...)".

   ![Výběr brány dat pro modul Import dat](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Zadejte **název a** **název databáze**databáze SQL database spolu s **dotazem databáze** SQL, který chcete spustit.
7. Klikněte na **Zadat hodnoty** v části **Uživatelské jméno a heslo** a zadejte přihlašovací údaje databáze. Integrované ověřování systému Windows nebo ověřování serveru SQL server můžete použít v závislosti na konfiguraci místního serveru SQL Server.

   ![Zadání pověření databáze](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Zpráva "požadované hodnoty" se změní na "nastavené hodnoty" se zeleným zaškrtnutím. Pověření je třeba zadat pouze jednou, pokud se nezmění informace o databázi nebo heslo. Azure Machine Learning Studio (klasické) používá certifikát, který jste zadali při instalaci brány k šifrování přihlašovacích údajů v cloudu. Azure nikdy neukládá místní přihlašovací údaje bez šifrování.

   ![Importovat vlastnosti modulu Dat](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Chcete-li experiment spustit, klepněte na tlačítko **SPUSTIT.**

Po dokončení experimentu můžete vizualizovat data importovaná z databáze klepnutím na výstupní port modulu **Importovat data** a výběrem **možnosti Vizualizovat**.

Po dokončení vývoje experimentu můžete nasadit a zprovoznit váš model. Pomocí služby dávkového spuštění budou data z místní databáze serveru SQL Server nakonfigurovaná v modulu **Import dat** přečtena a použita pro vyhodnocování. Zatímco službu požadavek na odezvu můžete použít pro vyhodnocování místních dat, společnost Microsoft doporučuje místo toho použít [doplněk aplikace Excel.](excel-add-in-for-web-services.md) V současné době není v experimentech nebo publikovaných webových službách podporováno zápis do místní databáze serveru SQL Server prostřednictvím **exportu dat.**
