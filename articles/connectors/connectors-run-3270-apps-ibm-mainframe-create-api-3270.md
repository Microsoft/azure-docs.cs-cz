---
title: Připojení k 3270 aplikacím na sálových počítači IBM
description: Integrace a automatizace 3270 aplikací řízených obrazovkami s Azure pomocí Azure Logic Apps a konektoru IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371109"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrace 3270 aplikací řízených obrazovkou na sálových počítači IBM s Azure pomocí Azure Logic Apps a konektoru IBM 3270

> [!NOTE]
> Tento konektor je ve [*verzi Public Preview*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

S Azure Logic Apps a konektorem IBM 3270 můžete přistupovat k mainframovým aplikacím IBM a spouštět je, které obvykle řídíte, a to procházením 3270 obrazovek emulátorů. Tímto způsobem můžete integrovat své mainframové aplikace IBM s Azure, Microsoftem a dalšími aplikacemi, službami a systémy vytvořením automatizovaných pracovních postupů pomocí Azure Logic Apps. Konektor komunikuje s sálovými počítači IBM pomocí protokolu TN3270 a je k dispozici ve všech oblastech Azure Logic Apps s výjimkou Azure Government a Azure China 21Vianet. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Tento článek popisuje tyto aspekty pro použití konektoru 3270: 

* Proč používat konektor IBM 3270 v Azure Logic Apps a jak konektor spouští 3270 aplikací řízených obrazovkou 3270

* Předpoklady a nastavení pro použití konektoru 3270

* Postup přidání akce konektoru 3270 do aplikace logiky

## <a name="why-use-this-connector"></a>Proč používat tento konektor?

Pro přístup k aplikacím na sálových počítači IBM obvykle používáte emulátor terminálu 3270, často nazývaný "zelená obrazovka". Tato metoda je časově testovaný způsob, ale má omezení. Přestože hostitelský integrační server (HIS) vám pomůže pracovat přímo s těmito aplikacemi, někdy oddělení obrazovky a obchodní logiky nemusí být možné. Nebo možná již nemáte informace o tom, jak hostitelské aplikace fungují.

Chcete-li rozšířit tyto scénáře, konektor IBM 3270 v Azure Logic Apps pracuje s 3270 Design Tool, který používáte k záznamu nebo "zachycení" hostitelských obrazovek používaných pro konkrétní úkol, definování toku navigace pro tento úkol prostřednictvím aplikace sálového počítače a definování se vstupními a výstupními parametry pro daný úkol. Návrhový nástroj převede tyto informace na metadata, která konektor 3270 používá při volání akce, která představuje tuto úlohu z aplikace logiky.

Po vygenerování souboru metadat z návrhového nástroje přidáte tento soubor do účtu integrace v Azure. Tímto způsobem vaše aplikace logiky přístup k metadatům vaší aplikace, když přidáte akci konektoru 3270. Spojnice přečte soubor metadat z vašeho účtu integrace, zpracovává navigaci přes 3270 obrazovek a dynamicky prezentuje parametry pro akci konektoru 3270. Potom můžete poskytnout data hostitelské aplikace a spojnice vrátí výsledky do aplikace logiky. Tímto způsobem můžete integrovat starší aplikace s Azure, Microsoft a dalšími aplikacemi, službami a systémy, které Azure Logic Apps podporuje.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Doporučeno: Prostředí [integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Toto prostředí můžete vybrat jako umístění pro vytváření a spouštění aplikace logiky. Služba ISE poskytuje přístup z vaší aplikace logiky k prostředkům, které jsou chráněné uvnitř virtuálních sítí Azure.

* Aplikace logiky, která se používá pro automatizaci a spuštění aplikace řízené obrazovkou 3270

  Konektor IBM 3270 nemá aktivační události, takže ke spuštění aplikace logiky použijte jinou aktivační událost, například aktivační událost **opakování.** Potom můžete přidat akce konektoru 3270. Chcete-li začít, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Pokud používáte ISE, vyberte tuto službu ISE jako umístění aplikace logiky.

* [Stáhněte a nainstalujte nástroj 3270 Design Tool](https://aka.ms/3270-design-tool-download).
Jediným předpokladem je [rozhraní Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Tento nástroj vám pomůže zaznamenat obrazovky, navigační cesty, metody a parametry pro úlohy v aplikaci, které přidáte a spustíte jako akce konektoru 3270. Nástroj generuje soubor XML (HIDX) návrháře integrace hostitele, který poskytuje potřebná metadata pro konektor, který se má použít pro řízení aplikace sálového počítače.
  
  Po stažení a instalaci tohoto nástroje postupujte takto pro připojení k hostiteli:

  1. Otevřete nástroj 3270 Design Tool. V nabídce **Relace** vyberte **Možnost Host Sessions**.
  
  1. Zadejte informace o hostitelském serveru TN3270.

* [Účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), což je místo, kde ukládáte soubor HIDX jako mapu, takže aplikace logiky může přistupovat k definicím metadat a metod v tomto souboru. 

  Ujistěte se, že váš účet integrace je propojený s aplikací logiky, kterou používáte. Také pokud používáte ISE, ujistěte se, že umístění účtu integrace je stejné ISE, které používá vaše aplikace logiky.

* Přístup k serveru TN3270, který hostuje vaši aplikaci sálových počítačů

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Vytvoření přehledu metadat

V aplikaci řízené obrazovkou 3270 jsou obrazovky a datová pole jedinečná pro vaše scénáře, takže konektor 3270 potřebuje tyto informace o vaší aplikaci, které můžete poskytnout jako metadata. Tato metadata popisují informace, které pomáhají vaší aplikaci logiky identifikovat a rozpoznat obrazovky, popisuje, jak procházet mezi obrazovkami, kde vstupní data a kde očekávat výsledky. Chcete-li určit a generovat tato metadata, použijte nástroj 3270 Design Tool, který vás provede těmito *specifickými režimy*nebo fázemi, jak je popsáno dále podrobněji:

* **Zachycení**: V tomto režimu zaznamenáte obrazovky potřebné pro dokončení určitého úkolu pomocí aplikace sálového počítače, například získání bankovního zůstatku.

* **Navigace**: V tomto režimu určíte plán nebo cestu pro navigaci na obrazovkách aplikace sálového počítače pro konkrétní úkol.

* **Metody**: V tomto režimu definujete `GetBalance`metodu, například , která popisuje navigační cestu obrazovky. Můžete také zvolit pole na každé obrazovce, které se stanou vstupní a výstupní parametry metody.

### <a name="unsupported-elements"></a>Nepodporované prvky

Návrhový nástroj nepodporuje tyto prvky:

* Částečné mapy základní podpory mapování IBM (BMS): Pokud importujete mapu BMS, návrhový nástroj ignoruje částečné definice obrazovky.
* Parametry In/Out: Nelze definovat parametry In/Out.
* Zpracování nabídky: Není podporováno během náhledu
* Zpracování pole: Není podporováno během náhledu

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Zachytávání obrazovek

V tomto režimu označíte položku na každé obrazovce 3270, která tuto obrazovku jednoznačně identifikuje. Můžete například zadat řádek textu nebo složitější sadu podmínek, například určitý text a neprázdné pole. Tyto obrazovky můžete zaznamenat přes živé připojení k hostitelskému serveru nebo tyto informace importovat z mapy Základní podpory mapování IBM (BMS). Živé připojení používá emulátor TN3270 pro připojení k hostiteli. Každá akce konektoru musí být mapována na jednu úlohu, která začíná připojením k relaci a končí odpojením od relace.

1. Pokud jste tak dosud neučinili, otevřete nástroj 3270 Design Tool. Na panelu nástrojů zvolte **Zachytit,** abyste vstoupili do režimu sběru.

1. Chcete-li zahájit nahrávání, stiskněte klávesu F5 nebo v nabídce **Nahrávání** vyberte **Spustit nahrávání**. 

1. V nabídce **Relace** vyberte **Připojit**.

1. V podokně **Digitalizace,** počínaje první obrazovkou v aplikaci, projděte aplikaci pro konkrétní úlohu, kterou nahráváte.

1. Po dokončení úkolu se odhlaste z aplikace obvyklým.

1. V nabídce **Relace** vyberte **Odpojit**.

1. Nahrávání zastavíte stisknutím kláves Shift + F5 nebo v nabídce **Nahrávání** vyberte **Zastavit nahrávání**.

   Po zachycení obrazovek pro úkol, nástroj návrháře zobrazí miniatury, které představují tyto obrazovky. Některé poznámky o těchto miniaturách:

   * Součástí zachycených obrazovek je obrazovka s názvem "Prázdný".

     Při prvním připojení k [CICS](https://www.ibm.com/it-infrastructure/z/cics)je nutné před zadáním názvu transakce, kterou chcete spustit, odeslat klíč Vymazat. Obrazovka, na které odešlete klávesu Vymazat, nemá žádné *atributy rozpoznávání*, například název obrazovky, který můžete přidat pomocí editoru rozpoznávání obrazovky. Chcete-li reprezentovat tuto obrazovku, miniatury obsahuje obrazovku s názvem "Prázdný". Později můžete tuto obrazovku použít pro reprezentaci obrazovky, na které zadáte název transakce.

   * Ve výchozím nastavení používá název zachycené obrazovky první slovo na obrazovce. Pokud tento název již existuje, návrhový nástroj připojí název podtržítkem a číslem, například "WBGB" a "WBGB_1".

1. Chcete-li zachycené obrazovce přidělit smysluplnější název, postupujte takto:

   1. V podokně **Hostitelské obrazovky** vyberte obrazovku, kterou chcete přejmenovat.

   1. Ve stejném podokně v dolní části ve stejném podokně najděte vlastnost **Název obrazovky.**

   1. Změňte aktuální název obrazovky na popisnější název.

1. Nyní zadejte pole pro identifikaci jednotlivých obrazovek.

   S datovým proudem 3270 nemají obrazovky výchozí identifikátory, takže na každé obrazovce je třeba vybrat jedinečný text. Pro složité scénáře můžete zadat více podmínek, například jedinečný text a pole s určitou podmínkou.

Po dokončení výběru polí rozpoznávání přejděte do dalšího režimu.

### <a name="conditions-for-identifying-repeated-screens"></a>Podmínky pro identifikaci opakovaných obrazovek

Pro konektor pro navigaci a rozlišení mezi obrazovkami, obvykle najít jedinečný text na obrazovce, které můžete použít jako identifikátor mezi zachycené obrazovky. U opakovaných obrazovek může být nutné použít další metody identifikace. Předpokládejme například, že máte dvě obrazovky, které vypadají stejně, s výjimkou jedné obrazovky vrátí platnou hodnotu, zatímco druhá obrazovka vrátí chybovou zprávu.

V návrhovém nástroji můžete pomocí editoru rozpoznávání přidat *atributy rozpoznávání*, například název obrazovky, například Získat zůstatek na účtu. Pokud máte rozvětvenou cestu a obě větve vrátí stejnou obrazovku, ale s různými výsledky, budete potřebovat další atributy rozpoznávání. V době běhu spojnice používá tyto atributy pro určení aktuální větve a rozvětvení. Zde jsou podmínky, které můžete použít:

* Určitá hodnota: Tato hodnota odpovídá zadanému řetězci v zadaném umístění.
* NENÍ určitá hodnota: Tato hodnota neodpovídá zadanému řetězci v zadaném umístění.
* Prázdné: Toto pole je prázdné.
* NENÍ prázdné: Toto pole není prázdné.

Další informace najdete v [tématu Příklad navigačního plánu](#example-plan) dále v tomto tématu.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definování navigačních plánů

V tomto režimu definujete tok nebo kroky pro navigaci na obrazovkách aplikace sálových počítačů pro konkrétní úkol. Někdy může mít například více než jednu cestu, kterou může vaše aplikace podniknout tam, kde jedna cesta vytváří správný výsledek, zatímco druhá cesta způsobí chybu. Pro každou obrazovku určete úhozy potřebné pro přechod `CICSPROD <enter>`na další obrazovku, například .

> [!TIP]
> Pokud automatizujete několik úloh, které používají stejné obrazovky pro připojení a odpojení, návrhový nástroj poskytuje speciální typy plánu Připojení a Odpojení. Když definujete tyto plány, můžete je přidat do začátku a konce navigačního plánu.

### <a name="guidelines-for-plan-definitions"></a>Pokyny pro definice plánu

* Zahrnout všechny obrazovky, počínaje připojením a konče odpojením.

* Můžete vytvořit samostatný plán nebo použít plány Připojit a odpojit, které umožňují znovu použít řadu obrazovek společných pro všechny transakce.

  * Poslední obrazovka v plánu Připojení musí být stejná jako první obrazovka navigačního plánu.

  * První obrazovka v plánu odpojení musí být stejná jako poslední obrazovka v navigačním plánu.

* Zachycené obrazovky mohou obsahovat mnoho opakovaných obrazovek, proto vyberte a použijte pouze jednu instanci všech opakovaných obrazovek v plánu. Zde je několik příkladů opakovaných obrazovek:

  * Přihlašovací obrazovka, například obrazovka **MSG-10**
  * Uvítací obrazovka pro CICS
  * Obrazovka "Vymazat" nebo **Prázdný**

<a name="create-plans"></a>

### <a name="create-plans"></a>Vytvořit plány

1. Na panelu nástrojů nástroje návrhu 3270 zvolte **Navigace,** abyste vstoupili do navigačního režimu.

1. Chcete-li plán spustit, zvolte v **navigačním** podokně **Nový plán**.

1. V **části Zvolte Nový název plánu**zadejte název plánu. Ze seznamu **Typ** vyberte typ plánu:

   | Typ plánu | Popis |
   |-----------|-------------|
   | **Proces** | Pro samostatné nebo kombinované plány |
   | **Připojit** | Pro plány Connect |
   | **Odpojit** | Pro plány odpojení |
   |||

1. V podokně **Hostitelské obrazovky** přetáhněte zachycené miniatury na povrch navigačního plánu v **navigačním** podokně.

   Chcete-li reprezentovat prázdnou obrazovku, na které zadáváte název transakce, použijte obrazovku "Prázdný".

1. Uspořádejte obrazovky v pořadí, které popisuje úkol, který definujete.

1. Chcete-li definovat cestu toku mezi obrazovkami, včetně vidliček a spojení, na panelu nástrojů nástroje návrhu zvolte **Tok**.

1. Zvolte první obrazovku v toku. Přetáhněte a nakreslete připojení k další obrazovce v toku.

1. Pro každou obrazovku zadejte hodnoty vlastnosti **KLÍČ AID** (Identifikátor pozornosti) a vlastnosti **Pevný text,** která přesune tok na další obrazovku.

   Je možné, že máte pouze klíč AID nebo klíč AID i pevný text.

Po dokončení navigačního plánu můžete [definovat metody v dalším režimu](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Příklad

V tomto příkladu předpokládejme, že spustíte transakci CICS s názvem "WBGB", která má tyto kroky: 

* Na první obrazovce zadáte jméno a účet.
* Na druhé obrazovce získáte zůstatek na účtu.
* Ukončíte se na obrazovku "Prázdný".
* Odhlásíte se z CICS na obrazovku "MSG-10".

Předpokládejme také, že tyto kroky zopakujete, ale zadáte nesprávná data, abyste mohli zachytit obrazovku, která zobrazuje chybu. Zde jsou obrazovky, které zachytíte:

* MSG-10
* CICS Vítejte
* Prázdné
* WBGB_1 (vstup)
* WBGB_2 (chyba)
* Empty_1
* MSG-10_1

Ačkoli mnoho obrazovek zde získat jedinečné názvy, některé obrazovky jsou stejné obrazovky, například "MSG-10" a "Prázdný". Pro opakovanou obrazovku použijte v plánu pouze jednu instanci pro tuto obrazovku. Zde jsou příklady, které ukazují, jak může vypadat samostatný plán, plán Připojení, Plán odpojení a kombinovaný plán:

* Samostatný plán

  ![Samostatný navigační plán](./media/connectors-create-api-3270/standalone-plan.png)

* Plán připojení

  ![Plán připojení](./media/connectors-create-api-3270/connect-plan.png)

* Plán odpojení

  ![Plán odpojení](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinovaný plán

  ![Kombinovaný plán](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Příklad: Identifikace opakovaných obrazovek

Pro konektor pro navigaci a rozlišení obrazovek, obvykle najít jedinečný text na obrazovce, které můžete použít jako identifikátor přes zachycené obrazovky. U opakovaných obrazovek může být nutné použít další metody identifikace. Ukázkový plán má rozdvojení, kde můžete získat obrazovky, které vypadají podobně. Jedna obrazovka vrátí zůstatek na účtu, zatímco druhá obrazovka vrátí chybovou zprávu.

Návrhový nástroj umožňuje přidat atributy rozpoznávání, například název obrazovky s názvem "Získat zůstatek na účtu", pomocí editoru rozpoznávání obrazovky. V případě s podobnými obrazovkami potřebujete další atributy. Za běhu konektor používá tyto atributy pro určení větve a rozvětvení.

* Ve větvi, která vrací platný vstup, což je obrazovka se zůstatkem účtu, můžete přidat pole, které má podmínku "není prázdný".

* Ve větvi, která se vrací s chybou, můžete přidat pole, které má "prázdnou" podmínku.

<a name="define-method"></a>

## <a name="define-methods"></a>Definovat metody

V tomto režimu definujete metodu, která je přidružena k navigačnímu plánu. Pro každý parametr metody zadáte datový typ, například řetězec, celé číslo, datum nebo čas a tak dále. Po dokončení můžete otestovat metodu na živém hostiteli a potvrdit, že metoda funguje podle očekávání. Potom vygenerujete soubor metadat nebo soubor XML (HIDX) návrháře integrace hostitele, který má nyní definice metod, které se mají použít pro vytvoření a spuštění akce pro konektor IBM 3270.

1. Na panelu nástrojů nástroje návrhu 3270 zvolte **Metody,** abyste vstoupili do režimu metody. 

1. V **navigačním** podokně vyberte obrazovku se vstupními poli, která chcete.

1. Chcete-li přidat první vstupní parametr pro metodu, postupujte takto:

   1. V podokně **Zachycení** na obrazovce emulátoru 3270 zvolte celé pole, nikoli pouze text uvnitř pole, který chcete jako první vstup.

      > [!TIP]
      > Chcete-li zobrazit všechna pole a ujistěte se, že jste vybrali celé pole, vyberte v nabídce **Zobrazení** **možnost Všechna pole**.

   1. Na panelu nástrojů nástroje návrhu zvolte **Vstupní pole**. 

   Chcete-li přidat další vstupní parametry, opakujte předchozí kroky pro každý parametr.

1. Chcete-li přidat první výstupní parametr pro metodu, postupujte takto:

   1. V podokně **Zachycení** na obrazovce emulátoru 3270 zvolte celé pole, nikoli pouze text uvnitř pole, který chcete jako první výstup.

      > [!TIP]
      > Chcete-li zobrazit všechna pole a ujistěte se, že jste vybrali celé pole, vyberte v nabídce **Zobrazení** **možnost Všechna pole**.

   1. Na panelu nástrojů nástroje návrhu zvolte **Výstupní pole**.

   Chcete-li přidat další výstupní parametry, opakujte předchozí kroky pro každý parametr.

1. Po přidání všech parametrů metody definujte tyto vlastnosti pro každý parametr:

   | Název vlastnosti | Možné hodnoty | 
   |---------------|-----------------|
   | **Typ dat** | Bajt, Čas data, Desetinné, Int, Dlouhé, Krátké, Řetězec |
   | **Technika plnění polí** | Parametry podporují tyto typy výplní, v případě potřeby vyplňují mezery: <p><p>- **Typ**: Postupně zadávejte znaky do pole. <p>- **Výplň**: Obsah pole nahraďte znaky, v případě potřeby vyplňte mezery. <p>- **Vymazat typ typu**: Zrušte zaškrtnutí pole a potom do pole postupně zadejte znaky. |
   | **Formátovací řetězec** | Některé datové typy parametrů používají formátovací řetězec, který informuje spojnici 3270 o převodu textu z obrazovky na datový typ .NET: <p><p>- **DateTime**: Formátovací řetězec DateTime následuje za [vlastními formátovacími řetězci .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Například datum `06/30/2019` používá formátovací `MM/dd/yyyy`řetězec . <p>- **Desetinné číslo**: Desetinný formátovací řetězec používá [klauzuli COBOL Picture](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Například číslo `100.35` používá formátovací `999V99`řetězec . |
   |||

## <a name="save-and-view-metadata"></a>Uložení a zobrazení metadat

Po definování metody, ale před testováním metody, uložte všechny informace, které jste dosud definovali, do souboru RAP (.rap).
Do tohoto rap ového souboru můžete kdykoli uložit v libovolném režimu. Návrhový nástroj také obsahuje ukázkový rap ový soubor, který můžete otevřít a zkontrolovat procházením instalační složky návrhového nástroje v tomto umístění a otevřením souboru "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Pokud se však pokusíte uložit změny do ukázkového souboru RAP nebo vygenerovat soubor HIDX z ukázkového souboru RAP, zatímco soubor zůstane v instalační složce návrhového nástroje, může se stát, že dojde k chybě "přístup byl odepřen". Ve výchozím nastavení se návrhový nástroj nainstaluje do složky Program Files bez zvýšených oprávnění. Pokud se zobrazí chyba, vyzkoušejte jedno z těchto řešení:

* Zkopírujte ukázkový soubor do jiného umístění.
* Spusťte návrhový nástroj jako správce.
* Udělejte si vlastníka složky SDK.

## <a name="test-your-method"></a>Otestujte svou metodu

1. Chcete-li spustit metodu proti živému hostiteli, zatímco jste ještě v režimu Metody, stiskněte klávesu F5 nebo na panelu nástrojů nástroje pro návrh zvolte **Spustit**.

   > [!TIP]
   > Režimy můžete kdykoli změnit. V nabídce **Soubor** vyberte **Režim**a pak vyberte požadovaný režim.

1. Zadejte hodnoty parametrů a zvolte **OK**.

1. Chcete-li pokračovat na další obrazovku, zvolte **Další**.

1. Po dokončení zvolte **Hotovo**, které zobrazuje hodnoty výstupních parametrů.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generovat a nahrát soubor HIDX

Až budete připraveni, vygenerujte soubor HIDX, abyste mohli nahrát do svého účtu integrace. Nástroj 3270 Design Tool vytvoří soubor HIDX v nové podsložce, do které jste uložili soubor RAP.

1. Na panelu nástrojů nástroje návrhu 3270 zvolte **Generovat kód**.

1. Přejděte do složky obsahující soubor RAP a otevřete podsložku, kterou nástroj vytvořil po vygenerování souboru HIDX. Zkontrolujte, zda nástroj vytvořil soubor HIDX.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a najděte svůj účet integrace.

1. Přidejte soubor HIDX jako mapu do účtu integrace [podle těchto podobných kroků pro přidání map](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ale když vyberete typ mapy, vyberte **HIDX**.

Později v tomto tématu při prvním přidání akce IBM 3270 do aplikace logiky budete vyzváni k vytvoření připojení mezi aplikací logiky a hostitelským serverem poskytnutím informací o připojení, jako jsou názvy vašeho účtu integrace a hostitelského serveru . Po vytvoření připojení můžete vybrat dříve přidaný soubor HIDX, metodu, která má být spuštěna, a parametry, které chcete použít.

Po dokončení všech těchto kroků můžete použít akci, kterou vytvoříte v aplikaci logiky pro připojení k mainframu IBM, obrazovky jednotek pro vaši aplikaci, zadávání dat, vrácení výsledků a tak dále. Můžete také pokračovat v přidávání dalších akcí do aplikace logiky pro integraci s jinými aplikacemi, službami a systémy.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Spuštění akcí IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**a vyberte **Přidat akci**. 

1. Pod vyhledávacím polem zvolte **Enterprise**. Do vyhledávacího pole zadejte jako filtr hodnotu "3270". Ze seznamu akcí vyberte tuto akci: **Spustí program sálových počítačů přes připojení TN3270.**

   ![Vybrat akci 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Pokud ještě žádné připojení neexistuje, zadejte potřebné informace pro připojení a zvolte **Vytvořit**.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | <*název připojení*> | Název připojení |
   | **ID integračního účtu** | Ano | <*integrace-název účtu*> | Název účtu pro integraci |
   | **Adresa URL účtu integrace SAS** | Ano | <*integrace-účet-SAS-URL*> | Adresa URL sdíleného přístupového podpisu (SAS) vašeho účtu integrace, kterou můžete generovat z nastavení účtu integrace na webu Azure Portal. <p>1. V nabídce integračního účtu vyberte v části **Nastavení** **adresu URL zpětného volání**. <br>2. V pravém podokně zkopírujte hodnotu **url generovaného zpětného volání.** |
   | **Server** | Ano | <*TN3270-název serveru*> | Název serveru pro vaši službu TN3270 |
   | **Port** | Ne | <*TN3270-server-port*> | Port používaný serverem TN3270. Pokud je ponecháno `23` prázdné, konektor používá jako výchozí hodnotu. |
   | **Typ zařízení** | Ne | <*IBM-terminálový model*> | Název nebo číslo modelu terminálu IBM, které má být emulováno. Pokud je konektor prázdný, použije výchozí hodnoty. |
   | **Code Page** | Ne | <*číslo znakové stránky*> | Číslo znakové stránky hostitele. Pokud je ponecháno `37` prázdné, konektor používá jako výchozí hodnotu. |
   | **Název logické jednotky** | Ne | <*logický název jednotky*> | Konkrétní název logické jednotky, který chcete požadovat od hostitele |
   | **Povolit SSL?** | Ne | Zapnutí nebo vypnutí | Zapněte nebo vypněte šifrování TLS. |
   | **Ověřit certifikát ssl hostitele?** | Ne | Zapnutí nebo vypnutí | Zapněte nebo vypněte ověřování certifikátu serveru. |
   ||||

   Například:

   ![Vlastnosti připojení](./media/connectors-create-api-3270/connection-properties.png)

1. Poskytněte potřebné informace pro akci:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Hidx jméno** | Ano | <*HIDX-název souboru*> | Vyberte soubor 3270 HIDX, který chcete použít. |
   | **Název metody** | Ano | <*název metody*> | Vyberte metodu v souboru HIDX, kterou chcete použít. Po výběru metody se zobrazí seznam **Přidat nový parametr,** abyste mohli vybrat parametry, které se mají použít s tou metodou. |
   ||||

   Například:

   **Výběr souboru HIDX**

   ![Vybrat soubor HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Vyberte metodu**

   ![Vybrat metodu](./media/connectors-create-api-3270/select-method.png)

   **Vyberte parametry**

   ![Výběr parametrů](./media/connectors-create-api-3270/add-parameters.png)

1. Až budete hotovi, uložte a spusťte aplikaci logiky.

   Po dokončení spuštění aplikace logiky se zobrazí kroky z běhu. 
   Úspěšné kroky zobrazit zaškrtnutí, zatímco neúspěšné kroky zobrazit písmeno "X".

1. Chcete-li zkontrolovat vstupy a výstupy pro každý krok, rozbalte tento krok.

1. Chcete-li zkontrolovat výstupy, zvolte **Zobrazit nezpracované výstupy**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/si3270/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
