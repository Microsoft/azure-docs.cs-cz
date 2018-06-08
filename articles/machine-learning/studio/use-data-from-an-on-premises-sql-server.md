---
title: Použít místní SQL Server v Azure Machine Learning | Microsoft Docs
description: Pomocí dat z databáze SQL serveru místní provádět pokročilé analýzy pomocí Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: c3402c824b70d357b68e71ed7cb5783a7489b2b0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837374"
---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Provádění pokročilých analýz dat místní databáze SQL Serveru pomocí služby Azure Machine Learning

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Často podnikům, které pracují s místními daty chtěli využívat měřítka a flexibility cloudu pro jejich strojového učení úlohy. Ale nechcete použít narušit jejich aktuální podnikové procesy a pracovní postupy přesunutím jejich místní data do cloudu. Azure Machine Learning teď podporuje čtení dat z databáze SQL serveru na místě a pak školení a vyhodnocování model se tato data. Už máte ručně zkopírujte a synchronizaci dat mezi cloudu a místní server. Místo toho **importovat Data** modulu v Azure Machine Learning Studio nyní může načíst přímo z vaší místní databáze SQL serveru pro váš školení a vyhodnocování úlohy.

Tento článek obsahuje přehled o tom, jak příchozího místní data SQL serveru do Azure Machine Learning. Předpokládá, že jste obeznámeni s koncepty Azure Machine Learning pracovních prostorů, moduly, datové sady, experimenty, *atd*.

> [!NOTE]
> Tato funkce není k dispozici pro bezplatné pracovní prostory. Další informace o cenách Machine Learning a úrovně najdete v tématu [Azure Machine Learning – ceny](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>Nainstalujte bránu pro správu dat společnosti Microsoft
Pro přístup místní databázi systému SQL Server v Azure Machine Learning, musíte stáhnout a nainstalovat bránu pro správu dat společnosti Microsoft. Když konfigurujete připojení brány v nástroji Machine Learning Studio, máte možnost stáhnout a nainstalovat bránu **stahování a registrace brány dat** dialogové okno popsané dole.

Také můžete nainstalovat brána pro správu dat předem stažení a spuštění instalačního balíčku MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).
Vyberte nejnovější verzi, výběr 32bitové nebo 64bitové vhodnou pro váš počítač. Soubor MSI můžete také použít k upgradu existující brána správy dat na nejnovější verzi, se všemi možnými nastavení zachovaná.

Bránu má následující požadavky:

* Podporované verze operačního systému Windows jsou Windows 7, Windows 8 nebo 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Doporučenou konfiguraci pro počítače brány je alespoň 2 GHz, 4 jádra, 8GB paměti RAM a 80 GB místa na disku.
* Pokud hostitelský počítač přejde do režimu spánku, brána nebude odpovídat na požadavky na data. Proto konfigurovat plán odpovídající napájení v počítači před instalací brány. Pokud je počítač nakonfigurovaný do režimu spánku, brána instalace zobrazí zprávu.
* Protože aktivity kopírování dojde k určité frekvencí, využití prostředků (procesor, paměť) na počítači také používá se stejný vzor s ve špičce a doby nečinnosti. Využití prostředků závisí také výraznou na množství dat přesouvá. Pokud více úloh kopie jsou v průběhu, budete sledovat využití prostředků během špiček přejděte. Minimální konfigurace uvedené výše je technicky dostatečná, můžete v konfiguraci s více prostředků než minimální konfigurace v závislosti na konkrétní zatížení pro přesun dat.

Vezměte v úvahu následující při nastavování a používání Brána pro správu dat:

* Můžete nainstalovat pouze jedna instance brány pro správu dat v jednom počítači.
* Jedna brána můžete použít pro více zdrojů dat na místě.
* Více bran na různých počítačích můžete připojit ke stejnému zdroji dat na místě.
* Nakonfigurovat bránu pouze jednoho pracovního prostoru v čase. V současné době brány se nedají sdílet mezi pracovních prostorů.
* Můžete nakonfigurovat více bran pro jednoho pracovního prostoru. Můžete například použít bránu, která je připojena k testu zdroje dat během vývoje a produkční brány, až budete připraveni zprovoznit.
* Brána nemusí být ve stejném počítači jako zdroj dat. Ale zachování co nejblíže ke zdroji dat se snižuje čas pro bránu pro připojení ke zdroji dat. Doporučujeme nainstalovat bránu na počítači, který se liší od toho, který je hostitelem místní zdroj dat tak, aby brána a datový zdroj není pokouší pro prostředky.
* Pokud je již nainstalovaná ve vašem počítači obsluhující Power BI nebo Azure Data Factory scénáře, nainstalujte samostatnou bránu pro Azure Machine Learning na jiném počítači.

  > [!NOTE]
  > Brána pro správu dat a Power BI Gateway nelze spustit na stejném počítači.
  >
  >
* Budete muset použít Brána pro správu dat pro Azure Machine Learning, i když používáte Azure ExpressRoute pro další data. Zdroje dat by měly zpracovávat jako místní zdroj dat (který je za bránou firewall) i při použití ExpressRoute. Brána pro správu dat použijte k navázání připojení mezi Machine Learning a zdroj dat.

Podrobné informace o požadavky pro instalaci, kroky instalace a tipy k řešení potíží najdete v článku [Brána pro správu dat](../../data-factory/v1/data-factory-data-management-gateway.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Příchozí přenos dat z vaší místní databáze SQL serveru do Azure Machine Learning
V tomto návodu nastavit Brána pro správu dat v pracovním prostoru Azure Machine Learning, nakonfigurovat ji a potom číst data z databáze SQL serveru místní.

> [!TIP]
> Než začnete, zakažte blokování automaticky otevíraných oken v prohlížeči pro `studio.azureml.net`. Pokud používáte prohlížeč Google Chrome, stáhněte a nainstalujte jedním z několika moduly plug-in k dispozici na webu Google Chrome WebStore [klikněte jednou rozšíření aplikace](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
>

### <a name="step-1-create-a-gateway"></a>Krok 1: Vytvoření brány
Prvním krokem je vytvoření a nastavení brány pro přístup k vaší databázi SQL na místě.

1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/Home/) a vyberte pracovní prostor, který chcete, aby fungovaly v.
2. Klikněte na **nastavení** okno na levé straně a klikněte **brány DATA GATEWAYS** v horní části.
3. Klikněte na tlačítko **novou BRÁNU dat** v dolní části obrazovky.

    ![Nová brána dat](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. V **novou bránu dat** dialogové okno, zadejte **název brány** a volitelně přidejte **popis**. Klikněte na šipku v pravém dolním přejít k dalšímu kroku konfigurace.

    ![Zadejte název brány a popis](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Stažení a zaregistrujte bránu dialog datových zkopírujte do schránky registrační klíč brány.

    ![Stáhněte si a registraci brány dat](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Pokud jste ještě stáhli a nainstalovali jste Brána pro správu dat společnosti Microsoft, klepněte na tlačítko **Brána pro správu dat stažení**. Tím přejdete na Microsoft Download Center, kde můžete vybrat verzi brány, kterou budete potřebovat, stáhněte ho a nainstalujte ji. Podrobné informace o požadavky pro instalaci, kroky instalace a tipy k řešení potíží najdete v částech začátku článku [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) .
7. Poté, co je brána nainstalovaná, otevře se Správce konfigurace brány pro správu dat a **registrace brány** se zobrazí dialogové okno. Vložení **registrační klíč brány** jste zkopírovali do schránky a klikněte na tlačítko **zaregistrovat**.
8. Pokud již máte nainstalovaná, spusťte Správce konfigurace brány pro správu dat. Klikněte na tlačítko **změnit klíč**, vložte **registrační klíč brány** který jste zkopírovali do schránky v předchozím kroku a klikněte na tlačítko **OK**.
9. Po dokončení instalace **registrace brány** zobrazí dialog pro Microsoft Data Management Gateway Configuration Manager. Vložte registrační klíč brány, kterou jste zkopírovali do schránky v předchozím kroku a klikněte na tlačítko **zaregistrovat**.

    ![Registraci brány](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Když následující hodnoty jsou nastavené na dokončení konfigurace brány **Domů** kartě v Microsoft Data Management Gateway Configuration Manager:

    * **Název brány** a **název Instance** jsou nastavena na název brány.
    * **Registrace** je nastaven na **registrovaná**.
    * **Stav** je nastaven na **Začínáme**.
    * Stavový řádek v dolní zobrazí **připojené ke cloudové službě brány správy dat** společně s zelená značka zaškrtnutí.

      ![Správce brány pro správu dat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio získá také aktualizovat, po úspěšné registraci.

    ![Registrace brány úspěšné](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. V **stáhnout a bránu dat zaregistrujte** dialogové okno, kliknutím na značku zaškrtnutí dokončete nastavení. **Nastavení** stránce se zobrazuje stav brány jako "Online". V pravém podokně zjistíte stav a další užitečné informace.

    ![Nastavení brány](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. V Microsoft Data Management Gateway Správce konfigurace přepnout **certifikát** kartě. Certifikát uvedený na této kartě se používá k šifrování a dešifrování přihlašovací údaje pro místní datové úložiště, které zadáte na portálu. Tento certifikát je výchozí certifikát. Společnost Microsoft doporučuje, tato změna na vlastní certifikát, který zálohování v systému správy certifikátů. Klikněte na tlačítko **změnu** místo toho použít vlastní certifikát.

    ![Certifikát brány změn](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (volitelné) Pokud chcete povolit podrobné protokolování při řešení problémů s bránou v Microsoft Data Management Gateway Správce konfigurace přepnout **diagnostiky** kartě a zkontrolujte **povolit podrobné protokolování pro účely odstraňování potíží** možnost. Informace o protokolování naleznete v prohlížeči událostí systému Windows v části **protokoly aplikací a služeb**  - &gt; **Brána pro správu dat** uzlu. Můžete také **diagnostiky** kartě, abyste otestovali připojení ke zdroji dat místní používání brány.

    ![Povolit podrobné protokolování](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Dokončení procesu instalace brány v Azure Machine Learning.
Nyní jste připraveni použít místní data.

Můžete vytvořit a nastavit více bran v Studio pro každý pracovní prostor. Například může mít bránu, která chcete připojit ke zdrojům dat vaše testovací během vývoje a jinou bránu pro zdroje dat produkční. Azure Machine Learning vám dává možnost nastavit více bran, v závislosti na vašem podnikovém prostředí. V současné době nelze sdílet brána mezi pracovními prostory a pouze jeden gateway se dá nainstalovat v jednom počítači. Další informace najdete v tématu [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Použití brány číst data z místního zdroje dat
Po nastavení brány můžete přidat **importovat Data** modulu experimentu vstup data z databáze systému SQL Server na místě.

1. V nástroji Machine Learning Studio, vyberte **EXPERIMENTY** , klikněte na **+ nový** v levém dolním rohu a vyberte **prázdný Experiment** (nebo vybrat některou z několika ukázka experimentů k dispozici).
2. Najděte a přetáhněte **importovat Data** modulu na plátno experimentu.
3. Klikněte na tlačítko **uložit jako** níže na plátno. Zadejte "Azure kurz strojového učení místní SQL Server" pro název experimentu, vyberte pracovní prostor a klikněte **OK** zaškrtnutí.

   ![Uložte s novým názvem experimentu](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klikněte na tlačítko **importovat Data** modulu, pak vyberte v **vlastnosti** v podokně napravo od plátna vyberte "Místní databáze SQL" **zdroj dat** rozevíracího seznamu.
5. Vyberte **bránu Data gateway** instalaci a registraci. Jinou bránu můžete nastavit tak, že vyberete "(Přidat novou bránu dat...)".

   ![Vyberte bránu dat pro Import dat modul](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Zadejte SQL **název databázového serveru** a **název databáze**, spolu s SQL **databázový dotaz** chcete provést.
7. Klikněte na tlačítko **zadejte hodnoty** pod **uživatelské jméno a heslo** a zadejte své přihlašovací údaje databáze. Můžete použít integrované ověřování systému Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.

   ![Zadejte přihlašovací údaje databáze](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Změny zpráva "hodnoty požadované" do "Sada hodnoty" Zelená značka zaškrtnutí. Potřebujete jenom jednou zadejte přihlašovací údaje, dokud se informace o databázi nebo heslo nezmění. Azure Machine Learning používá certifikát, který jste zadali při instalaci brány k šifrování přihlašovacích údajů v cloudu. Azure nikdy ukládá místních přihlašovacích údajů bez šifrování.

   ![Import modulu vlastnosti dat](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klikněte na tlačítko **spustit** ke spuštění experimentu.

Po dokončení spuštění experimentu, můžete vizualizovat data importovat z databáze kliknutím na výstupní port modulu **importovat Data** modulu a výběrem **vizualizovat**.

Jakmile dokončíte vývoj experimentu, můžete nasadit a zprovoznit model. Pomocí služby Batch provádění, data z místního serveru SQL databáze nakonfigurované v **importovat Data** modul bude číst a používat pro vyhodnocování. I když můžete používat službu žádosti o odpovědi pro vyhodnocování místní data, společnost Microsoft doporučuje používat [doplněk aplikace Excel](excel-add-in-for-web-services.md) místo. V současné době zápisu do místní databáze systému SQL Server prostřednictvím **Export dat** není podporované buď v experimentů nebo publikované webové služby.
