---
title: 'ML Studio (Classic): místní SQL Server – Azure'
description: Použijte data z databáze SQL Server k provádění pokročilých analýz s využitím Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 8cdf1029371e0e11c38616e7800652ca9debbba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517395"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-a-sql-server-database"></a>Analýza pomocí SQL Server databáze pomocí Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Často by podniky, které pracují s místními daty, mohly využít výhod škály a flexibility cloudu pro úlohy strojového učení. Ale nechtějí přerušit své současné obchodní procesy a pracovní postupy přesunutím místních dat do cloudu. Azure Machine Learning Studio (Classic) nyní podporuje čtení dat z databáze SQL Server a následné školení a vyhodnocování modelu s těmito daty. Už nemusíte ručně kopírovat a synchronizovat data mezi cloudem a místním serverem. Místo toho se teď může modul **importu dat** v Azure Machine Learning Studio (Classic) přečíst přímo z databáze SQL Server pro vaše školicí a vyhodnocovací úlohy.

Tento článek poskytuje přehled způsobů příchozího SQL Server dat do Azure Machine Learning Studio (Classic). Předpokládá, že jste obeznámeni se základními koncepty studia, jako jsou pracovní prostory, moduly, datové sady, experimenty *atd.*

> [!NOTE]
> Tato funkce není k dispozici pro bezplatné pracovní prostory. Další informace o cenách a úrovních Machine Learning najdete v tématu [Azure Machine Learning ceny](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalace Integration Runtime Data Factory v místním prostředí
Chcete-li získat přístup k databázi SQL Server v Azure Machine Learning Studio (Classic), je nutné stáhnout a nainstalovat Data Factory v místním prostředí Integration Runtime, dříve označované jako brána Správa dat. Když nakonfigurujete připojení v Machine Learning Studio (Classic), máte možnost stáhnout a nainstalovat Integration Runtime (IR) pomocí dialogu **Stáhnout a zaregistrovat data Gateway** , který je popsaný níže.


Pomocí stažení a spuštění instalačního balíčku MSI z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717)můžete také nainstalovat prostředí IR před časem. Soubor MSI se dá použít taky k upgradu existujícího IR na nejnovější verzi, kde se zachovají všechna nastavení.

Data Factory Self-Hosted Integration Runtime má následující požadavky:

* Integrace Data Factory Self-Hosted vyžaduje 64 operační systém s .NET Framework 4.6.1 nebo vyšším.
* Podporované verze operačních systémů Windows jsou Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Doporučená konfigurace pro počítač s INFRAČERVENým přenosem je minimálně 2 GHz, 4 jádro procesoru, 8 GB paměti RAM a disk pro 80 GB.
* Pokud se hostitelský počítač přepne do režimu hibernace, přestane IR reagovat na požadavky na data. Proto nakonfigurujte v počítači vhodné schéma napájení před instalací IR. Pokud je počítač nakonfigurovaný do režimu hibernace, zobrazí se při instalaci infračerveného přenosu zpráva.
* Vzhledem k tomu, že aktivita kopírování probíhá na konkrétní frekvenci, využívá využití prostředků (CPU, paměť) na počítači stejný vzor s špičkou a nečinnými časy. Využití prostředků také závisí na množství dat, která se přesunují. Když probíhají více úloh kopírování, sledujte využití prostředků v době špičky. I když minimální výše uvedená konfigurace je technicky dostačující, možná budete chtít mít konfiguraci s více prostředky než minimální konfigurací v závislosti na konkrétní zátěži pro přesun dat.

Při nastavování a používání Integration Runtime Data Factory v místním prostředí Vezměte v úvahu následující:

* V jednom počítači můžete nainstalovat jenom jednu instanci prostředí IR.
* Pro více místních zdrojů dat můžete použít jeden infračervený.
* K místnímu zdroji dat můžete připojit více úřadů v různých počítačích.
* V jednu chvíli nakonfigurujete finanční úřad jenom pro jeden pracovní prostor. V současné době se finanční úřad nedá sdílet mezi pracovními prostory.
* Pro jeden pracovní prostor můžete nakonfigurovat více úřadů. Například můžete chtít použít IR, který je připojený k vašim testovacím zdrojům dat během vývoje, a provozní IR, až budete připraveni k zprovoznění.
* Prostředí IR nemusí být ve stejném počítači jako zdroj dat. Ale zachování bližšího zdroje dat zkracuje čas, kdy se brána připojí ke zdroji dat. Doporučujeme nainstalovat technologii IR na počítač, který je jiný než ten, který je hostitelem místního zdroje dat, aby brána a zdroj dat nesoutěží o prostředky.
* Pokud již máte v počítači nainstalováno INFRAČERVENé prostředí, které obsluhuje Power BI nebo Azure Data Factorych scénářů, nainstalujte samostatný IR pro Azure Machine Learning Studio (Classic) v jiném počítači.

  > [!NOTE]
  > Nemůžete spouštět Data Factory v místním prostředí Integration Runtime a Power BI Gateway na stejném počítači.
  >
  >
* Je nutné použít Data Factory Integration Runtime v místním prostředí pro Azure Machine Learning Studio (Classic) i v případě, že používáte Azure ExpressRoute pro jiná data. Zdroj dat byste měli považovat za místní zdroj dat (to je za bránou firewall), i když používáte ExpressRoute. K navázání spojení mezi Machine Learning a zdrojem dat použijte Integration Runtime Data Factory v místním prostředí.

Podrobné informace o požadavcích na instalaci, krocích instalace a tipůch k odstraňování potíží najdete v článku [Integration runtime v Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Příchozí data z databáze SQL Server do Azure Machine Learning
V tomto návodu nastavíte Integration Runtime Azure Data Factory v pracovním prostoru Azure Machine Learning, nakonfigurujete ho a potom načtete data z databáze SQL Server.

> [!TIP]
> Než začnete, zakažte blokování automaticky otevíraných oken v prohlížeči `studio.azureml.net` . Pokud používáte prohlížeč Google Chrome, Stáhněte a nainstalujte jeden z několika modulů plug-in, které jsou k dispozici na webu Google Chrome webstore [klikněte jednou na rozšíření aplikace](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory Integration Runtime v místním prostředí se dřív jmenovala jako Správa dat brána. V tomto kurzu se v tomto kroku budou dál odkazovat jako na bránu.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Vytvoření brány
Prvním krokem je vytvoření a nastavení brány pro přístup k databázi SQL.

1. Přihlaste se k [Azure Machine Learning Studio (Classic)](https://studio.azureml.net/Home/) a vyberte pracovní prostor, ve kterém chcete pracovat.
2. Klikněte na levé okno **Nastavení** a pak klikněte na kartu **brány dat** v horní části.
3. V dolní části obrazovky klikněte na **Nová brána dat** .

    ![Nová brána dat](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. V dialogovém okně **Nová brána dat** zadejte **název brány** a volitelně přidejte **Popis**. Kliknutím na šipku v pravém dolním rohu přejdete k dalšímu kroku konfigurace.

    ![Zadejte název a popis brány.](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. V dialogovém okně stáhnout a zaregistrovat datovou bránu zkopírujte registrační klíč brány do schránky.

    ![Stažení a registrace brány dat](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Pokud jste ještě nestáhli a nainstalovali bránu Microsoft Správa dat Gateway, klikněte na **Stáhnout bránu pro správu dat**. Tím přejdete na Microsoft Download Center, kde můžete vybrat požadovanou verzi brány, stáhnout ji a nainstalovat ji. Podrobné informace o požadavcích na instalaci, krocích instalace a tipů pro řešení potíží najdete v části úvodní části článku [přesun dat mezi místními zdroji a cloudem pomocí služby Správa dat Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Po instalaci brány se otevře Configuration Manager brány Správa dat a zobrazí se dialogové okno **zaregistrovat bránu** . Vložte **registrační klíč brány** , který jste zkopírovali do schránky, a klikněte na **zaregistrovat**.
8. Pokud už máte bránu nainstalovanou, spusťte Configuration Manager Správa dat brány. Klikněte na **změnit klíč**, vložte **registrační klíč brány** , který jste zkopírovali do schránky v předchozím kroku, a klikněte na **OK**.
9. Po dokončení instalace se zobrazí dialogové okno **zaregistrovat bránu** pro bránu Microsoft Správa dat gateway Configuration Manager. Vložte registrační klíč brány, který jste zkopírovali do schránky v předchozím kroku, a klikněte na **zaregistrovat**.

    ![Registrace brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfigurace brány je dokončena, když jsou na kartě **Domů** v bráně Microsoft Správa dat Configuration Manager Gateway nastaveny následující hodnoty:

    * Název **brány** a **název instance** jsou nastavené na název brány.
    * **Registrace** je nastavená na **zaregistrovaná**.
    * **Stav** je nastaven na **spuštěno**.
    * Stavový řádek v dolní části zobrazuje **připojení ke cloudové službě Správa dat Gateway** spolu se zeleným znakem zaškrtnutí.

      ![Správce brány Správa dat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (Classic) se také aktualizují po úspěšné registraci.

    ![Úspěšná registrace brány](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. V dialogovém okně **Stáhnout a zaregistrovat datovou bránu** kliknutím na značku zaškrtnutí dokončete instalaci. Na stránce **Nastavení** se zobrazí stav brány jako "online". V pravém podokně najdete stav a další užitečné informace.

    ![Nastavení brány](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. V bráně Microsoft Správa dat Configuration Manager přepněte na kartu **certifikát** . Certifikát zadaný na této kartě slouží k šifrování/dešifrování přihlašovacích údajů pro místní úložiště dat, které zadáte na portálu. Tento certifikát je výchozím certifikátem. Microsoft doporučuje tuto změnu na vlastní certifikát, který zálohujete v systému správy certifikátů. Pokud místo toho chcete použít vlastní certifikát, klikněte na **změnit** .

    ![Změnit certifikát brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. volitelné Pokud chcete povolit podrobné protokolování, abyste mohli řešit problémy s bránou, přejděte v bráně Microsoft Správa dat Configuration Manager na kartu **Diagnostika** a zaškrtněte možnost **Povolit podrobné protokolování pro účely řešení potíží** . Informace o protokolování najdete v Prohlížeč událostí Windows pod uzlem **protokoly aplikací a služeb**  - &gt; **Správa dat uzlu brány** . Kartu **Diagnostika** můžete také použít k otestování připojení k místnímu zdroji dat pomocí brány.

    ![Zapnout podrobné protokolování](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Tím se dokončí proces instalace brány v Azure Machine Learning Studio (Classic).
Teď jste připraveni použít vaše místní data.

Pro každý pracovní prostor můžete vytvořit a nastavit několik bran v nástroji Studio (Classic). Například můžete mít bránu, kterou chcete připojit ke zdrojům testovacích dat během vývoje, a jinou bránu pro zdroje dat v produkčním prostředí. Azure Machine Learning Studio (Classic) získáte flexibilitu při nastavení několika bran v závislosti na vašem podnikovém prostředí. V tuto chvíli nemůžete sdílet bránu mezi pracovními prostory a v jednom počítači může být nainstalovaná jenom jedna brána. Další informace najdete v tématu [přesun dat mezi místními zdroji a cloudem pomocí Správa dat brány](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: použití brány ke čtení dat z místního zdroje dat
Po nastavení brány můžete přidat modul **importu dat** do experimentu, který data z databáze SQL Server.

1. V Machine Learning Studio (Classic) vyberte kartu **experimenty** , v levém dolním rohu klikněte na **+ Nový** a vyberte možnost **prázdný experiment** (nebo vyberte jednu z několika možných experimentů s ukázkou).
2. Vyhledejte a přetáhněte na plátno experimentu modul **Import dat** .
3. Klikněte na tlačítko **Uložit** pod plátnem. Zadejte "Azure Machine Learning Studio (klasický) místní SQL Server kurz" pro název experimentu vyberte pracovní prostor a klikněte na značku zaškrtnutí **OK** .

   ![Uložit experiment s novým názvem](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknutím na modul **Import dat** ho vyberte a potom v podokně **vlastnosti** napravo od plátna vyberte v rozevíracím seznamu **zdroj dat** možnost místní SQL Database.
5. Vyberte **bránu dat** , kterou jste nainstalovali a zaregistrovali. Jinou bránu můžete nastavit tak, že vyberete (Přidat novou bránu dat).

   ![Vybrat bránu dat pro modul import dat](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Zadejte **název serveru SQL Database** a **název databáze** společně s **dotazem SQL Database** , který chcete spustit.
7. V části **uživatelské jméno a heslo** klikněte na **zadat hodnoty** a zadejte svoje přihlašovací údaje do databáze. V závislosti na tom, jak je SQL Server nakonfigurovaná, můžete použít integrované ověřování systému Windows nebo SQL Server ověřování.

   ![Zadat přihlašovací údaje databáze](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   U zprávy "požadované hodnoty" se změní hodnota nastavená na zelenou značku zaškrtnutí. Přihlašovací údaje stačí zadat jenom jednou, pokud se nezmění informace nebo heslo databáze. Azure Machine Learning Studio (Classic) používá certifikát, který jste zadali při instalaci brány pro šifrování přihlašovacích údajů v cloudu. Azure nikdy neukládá místní přihlašovací údaje bez šifrování.

   ![Importovat vlastnosti datového modulu](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknutím na **Spustit** spustíte experiment.

Po dokončení experimentu můžete vizualizovat data, která jste importovali z databáze, kliknutím na výstupní port modulu **Import dat** a výběrem možnosti **vizualizovat**.

Až dokončíte vývoj experimentů, můžete model nasadit a zprovoznění. Pomocí služby Batch Execution se data z databáze SQL Server nakonfigurovaná v modulu **Import dat** přečtou a použijí pro vyhodnocování. I když můžete použít službu Request response pro bodování místních dat, Microsoft doporučuje místo toho použít [doplněk Excelu](excel-add-in-for-web-services.md) . V současné době se zápis do databáze SQL Server prostřednictvím **exportu dat** nepodporuje ani v experimentech nebo publikovaných webových službách.