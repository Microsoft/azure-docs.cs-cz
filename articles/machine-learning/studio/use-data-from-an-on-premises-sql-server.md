---
title: Na místním SQL serveru
titleSuffix: ML Studio (classic) - Azure
description: Použijte data z místní databáze SQL Server k provádění pokročilých analýz s využitím Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204178"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Analýza pomocí Azure Machine Learning Studio (Classic) pomocí místní databáze SQL Server

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Často podniky, které fungují s místními daty by chtěli využívat škálování a flexibility cloudu k jejich počítači strojové učení. Ale nebudete chtít narušit jejich aktuální obchodní procesy a pracovní postupy díky přesunu jejich místní data do cloudu. Azure Machine Learning Studio (Classic) teď podporuje čtení vašich dat z místní databáze SQL Server a následné školení a vyhodnocování modelu s těmito daty. Už máte ručně zkopírovat a synchronizovat data mezi cloudem a místním serverem. Místo toho se teď může modul **importu dat** v Azure Machine Learning Studio (Classic) přečíst přímo z místní databáze SQL Server pro vaše školicí a vyhodnocovací úlohy.

Tento článek poskytuje přehled o příchozím přenosu dat z místního SQL serveru do Azure Machine Learning Studio (Classic). Předpokládá, že jste obeznámeni se základními koncepty studia, jako jsou pracovní prostory, moduly, datové sady, experimenty *atd.*

> [!NOTE]
> Tato funkce není k dispozici pro bezplatné pracovní prostory. Další informace o cenách a úrovních Machine Learning najdete v tématu [Azure Machine Learning ceny](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalace prostředí Integration Runtime Data Factory
Chcete-li získat přístup k místní databázi SQL Server v Azure Machine Learning Studio (Classic), je nutné stáhnout a nainstalovat Data Factory v místním prostředí Integration Runtime, dříve označované jako brána Správa dat. Když nakonfigurujete připojení v Machine Learning Studio (Classic), máte možnost stáhnout a nainstalovat Integration Runtime (IR) pomocí dialogu **Stáhnout a zaregistrovat data Gateway** , který je popsaný níže.


Pomocí stažení a spuštění instalačního balíčku MSI z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717)můžete také nainstalovat prostředí IR před časem. Soubor MSI se dá použít taky k upgradu existujícího IR na nejnovější verzi, kde se zachovají všechna nastavení.

Data Factory modul Integration Runtime má následující požadavky:

* Integrace místním objekt pro vytváření dat vyžaduje 64-bit operační systém pomocí rozhraní .NET Framework 4.6.1 nebo vyšší.
* Podporované verze operačního systému Windows jsou Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Doporučená konfigurace pro počítač reakcí na Incidenty, je nejméně 2 GHz, 4 jádra procesoru, 8GB paměti RAM a 80GB disk.
* Pokud se hostitelský počítač přepne do režimu hibernace, přestane IR reagovat na požadavky na data. Proto konfigurovat schéma napájení příslušné v v počítači před instalací IR. Pokud je počítač nakonfigurovaný do režimu hibernace, instalace prostředí IR zobrazí zprávu.
* Vzhledem k tomu, že konkrétní frekvencí dojde k aktivitě kopírování, využití prostředků (procesor, paměť) na počítači také používá stejný vzor s ve špičce a doby nečinnosti. Využití prostředků také závisí do značné míry na množství dat, který se přesouvá. Když probíhají úlohy s více kopií, budete sledovat využití prostředků zvýší během špiček. Minimální konfigurace uvedené výše je technicky dostatečná, můžete chtít mít konfigurací s více prostředků, než minimální požadavky na konfiguraci v závislosti na konkrétní zatížení pro přesun dat.

Vezměte v úvahu následující při nastavování a pomocí Data Factory modul Integration Runtime:

* V jednom počítači můžete nainstalovat jenom jednu instanci reakcí na Incidenty.
* Jednotné prostředí IR můžete použít pro více zdrojů dat v místním prostředí.
* Více IRs na různé počítače můžete připojit ke stejnému zdroji dat místní.
* V jednu chvíli nakonfigurujete finanční úřad jenom pro jeden pracovní prostor. V současné době se finanční úřad nedá sdílet mezi pracovními prostory.
* Můžete nakonfigurovat více IRs pro jeden pracovní prostor. Například můžete chtít použít IR, který je připojený k vašim testovacím zdrojům dat během vývoje, a provozní IR, až budete připraveni k zprovoznění.
* Prostředí IR nemusí být ve stejném počítači jako zdroj dat. Ale zůstává blíž ke zdroji dat snižuje čas potřebný pro bránu pro připojení ke zdroji dat. Doporučujeme nainstalovat prostředí IR v počítači, který je jiný než ten, který je hostitelem místního zdroje dat tak, aby brána a zdroj dat není soutěží o prostředky.
* Pokud již máte v počítači nainstalováno INFRAČERVENé prostředí, které obsluhuje Power BI nebo Azure Data Factorych scénářů, nainstalujte samostatný IR pro Azure Machine Learning Studio (Classic) v jiném počítači.

  > [!NOTE]
  > Data Factory modul Integration Runtime a Power BI Gateway nelze spustit ve stejném počítači.
  >
  >
* Je nutné použít Data Factory Integration Runtime v místním prostředí pro Azure Machine Learning Studio (Classic) i v případě, že používáte Azure ExpressRoute pro jiná data. Zdroje dat by měly zpracovávat jako zdroj dat v místním (který je za bránou firewall) i při použití ExpressRoute. K navázání připojení mezi Machine Learning a zdroji dat pomocí Data Factory modul Integration Runtime.

Podrobné informace o požadavcích na instalaci, krocích instalace a tipůch k odstraňování potíží najdete v článku [Integration runtime v Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Příchozí data z místní databáze SQL Server do Azure Machine Learning
V tomto návodu nastavíte Integration Runtime Azure Data Factory v pracovním prostoru Azure Machine Learning, nakonfigurujete ho a pak načtete data z místní databáze SQL Server.

> [!TIP]
> Než začnete, zakažte blokování automaticky otevíraných oken v prohlížeči pro `studio.azureml.net`. Pokud používáte prohlížeč Google Chrome, Stáhněte a nainstalujte jeden z několika modulů plug-in, které jsou k dispozici na webu Google Chrome webstore [klikněte jednou na rozšíření aplikace](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Brána správy dat se dřív používal Azure Data Factory modul Integration Runtime. Podrobný kurz nadále označujeme jako bránu.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Vytvoření brány
Prvním krokem je vytvoření a nastavení brány pro přístup k vaší místní databáze SQL.

1. Přihlaste se k [Azure Machine Learning Studio (Classic)](https://studio.azureml.net/Home/) a vyberte pracovní prostor, ve kterém chcete pracovat.
2. Klikněte na levé okno **Nastavení** a pak klikněte na kartu **brány dat** v horní části.
3. V dolní části obrazovky klikněte na **Nová brána dat** .

    ![Novou bránu Data Gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. V dialogovém okně **Nová brána dat** zadejte **název brány** a volitelně přidejte **Popis**. Klikněte na šipku v pravém dolním rohu přejdete k dalšímu kroku konfigurace.

    ![Zadejte název brány a popis](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. V stahování a zaregistrujte data gateway dialogovém okně zkopírujte do schránky brány registrační klíč.

    ![Stáhněte si a registraci brány dat](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Pokud jste ještě nestáhli a nainstalovali bránu Microsoft Správa dat Gateway, klikněte na **Stáhnout bránu pro správu dat**. Tím přejdete na webu Microsoft Download Center kde můžete vybrat verze brány, které potřebujete, si ho stáhnout a nainstalovat ho. Podrobné informace o požadavcích na instalaci, krocích instalace a tipů pro řešení potíží najdete v části úvodní části článku [přesun dat mezi místními zdroji a cloudem pomocí služby Správa dat Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Po instalaci brány se otevře Configuration Manager brány Správa dat a zobrazí se dialogové okno **zaregistrovat bránu** . Vložte **registrační klíč brány** , který jste zkopírovali do schránky, a klikněte na **zaregistrovat**.
8. Pokud už máte nainstalovanou bránu, spusťte Správce konfigurace brány správy dat. Klikněte na **změnit klíč**, vložte **registrační klíč brány** , který jste zkopírovali do schránky v předchozím kroku, a klikněte na **OK**.
9. Po dokončení instalace se zobrazí dialogové okno **zaregistrovat bránu** pro bránu Microsoft Správa dat gateway Configuration Manager. Vložte registrační klíč brány, který jste zkopírovali do schránky v předchozím kroku, a klikněte na **zaregistrovat**.

    ![Registrace brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfigurace brány je dokončena, když jsou na kartě **Domů** v bráně Microsoft Správa dat Configuration Manager Gateway nastaveny následující hodnoty:

    * Název **brány** a **název instance** jsou nastavené na název brány.
    * **Registrace** je nastavená na **zaregistrovaná**.
    * **Stav** je nastaven na **spuštěno**.
    * Stavový řádek v dolní části zobrazuje **připojení ke cloudové službě Správa dat Gateway** spolu se zeleným znakem zaškrtnutí.

      ![Správce brány správy dat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (Classic) se také aktualizují po úspěšné registraci.

    ![Registrace brány byla úspěšná](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. V dialogovém okně **Stáhnout a zaregistrovat datovou bránu** kliknutím na značku zaškrtnutí dokončete instalaci. Na stránce **Nastavení** se zobrazí stav brány jako "online". V pravém podokně zjistíte, stav a další užitečné informace.

    ![Nastavení brány](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. V bráně Microsoft Správa dat Configuration Manager přepněte na kartu **certifikát** . Certifikát zadaný na této kartě slouží k šifrování/dešifrování přihlašovacích údajů pro místní úložiště dat, které zadáte na portálu. Tento certifikát je výchozí certifikát. Společnost Microsoft doporučuje změna na svůj vlastní certifikát, který je zálohovat v systému pro správu certifikátů. Pokud místo toho chcete použít vlastní certifikát, klikněte na **změnit** .

    ![Změnit certifikát brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. volitelné Pokud chcete povolit podrobné protokolování, abyste mohli řešit problémy s bránou, přejděte v bráně Microsoft Správa dat Configuration Manager na kartu **Diagnostika** a zaškrtněte možnost **Povolit podrobné protokolování pro účely řešení potíží** . Informace o protokolování najdete v Prohlížeč událostí Windows v části **protokoly aplikací a služeb** -&gt; uzlu **Správa dat brány** . Kartu **Diagnostika** můžete také použít k otestování připojení k místnímu zdroji dat pomocí brány.

    ![Zapnout podrobné protokolování](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Tím se dokončí proces instalace brány v Azure Machine Learning Studio (Classic).
Nyní jste připraveni používat vaše místní data.

Pro každý pracovní prostor můžete vytvořit a nastavit několik bran v nástroji Studio (Classic). Například může mít bránu, kterou chcete připojit ke zdrojům dat testu během vývoje a jinou bránu pro zdroje dat produkčního prostředí. Azure Machine Learning Studio (Classic) získáte flexibilitu při nastavení několika bran v závislosti na vašem podnikovém prostředí. V tuto chvíli nemůžete sdílet bránu mezi pracovními prostory a v jednom počítači může být nainstalovaná jenom jedna brána. Další informace najdete v tématu [přesun dat mezi místními zdroji a cloudem pomocí Správa dat brány](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Použití brány číst data z místních zdrojů dat
Po nastavení brány můžete přidat modul **importu dat** do experimentu, který bude pokaždé zadat data z místní databáze SQL Server.

1. V Machine Learning Studio (Classic) vyberte kartu **experimenty** , v levém dolním rohu klikněte na **+ Nový** a vyberte možnost **prázdný experiment** (nebo vyberte jednu z několika možných experimentů s ukázkou).
2. Vyhledejte a přetáhněte na plátno experimentu modul **Import dat** .
3. Klikněte na tlačítko **Uložit** pod plátnem. Zadejte "Azure Machine Learning Studio (klasický) místní SQL Server kurz" pro název experimentu vyberte pracovní prostor a klikněte na značku zaškrtnutí **OK** .

   ![Uložit experimentu s novým názvem.](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknutím na modul **Import dat** ho vyberte a potom v podokně **vlastnosti** napravo od plátna vyberte v rozevíracím seznamu **zdroj dat** možnost místní SQL Database.
5. Vyberte **bránu dat** , kterou jste nainstalovali a zaregistrovali. Jinou bránu můžete nastavit tak, že vyberete "(Přidat novou bránu Data Gateway...)".

   ![Vyberte bránu dat pro modulu Import dat](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Zadejte **název serveru SQL Database** a **název databáze**společně s **dotazem SQL Database** , který chcete spustit.
7. V části **uživatelské jméno a heslo** klikněte na **zadat hodnoty** a zadejte svoje přihlašovací údaje do databáze. Můžete použít integrované ověřování Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.

   ![Zadejte přihlašovací údaje databáze](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Změny "hodnoty požadované" zpráva "sada hodnot" s zelená značka zaškrtnutí. Stačí zadat přihlašovací údaje jednou, pokud se změní informace o databázi nebo heslo. Azure Machine Learning Studio (Classic) používá certifikát, který jste zadali při instalaci brány pro šifrování přihlašovacích údajů v cloudu. Azure nikdy ukládá místních přihlašovacích údajů bez šifrování.

   ![Import modulu vlastnosti dat](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknutím na **Spustit** spustíte experiment.

Po dokončení experimentu můžete vizualizovat data, která jste importovali z databáze, kliknutím na výstupní port modulu **Import dat** a výběrem možnosti **vizualizovat**.

Jakmile dokončíte, vývoji experimentů, můžete nasazení a zprovoznění vašeho modelu. Pomocí služby Batch Execution se data z místní databáze SQL Server nakonfigurovaná v modulu **Import dat** přečtou a budou se používat pro vyhodnocování. I když můžete použít službu Request response pro bodování místních dat, Microsoft doporučuje místo toho použít [doplněk Excelu](excel-add-in-for-web-services.md) . V současné době se zápisy do místní databáze SQL Server prostřednictvím **exportu dat** nepodporují ani v experimentech nebo publikovaných webových službách.
