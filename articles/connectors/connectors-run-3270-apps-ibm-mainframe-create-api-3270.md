---
title: Připojte se k aplikacím 3270 na sálové počítače IBM s Azure – Azure Logic Apps
description: Integrujte a Automatizujte 3270 aplikací založených na obrazovce pomocí Azure s využitím konektoru Azure Logic Apps a IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 7388dc0c61dad9c31da0c178febcee4c8481bc50
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313188"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integraci 3270 aplikace řízené modelem obrazovky na sálové počítače IBM v Azure s využitím konektoru Azure Logic Apps a IBM 3270

> [!NOTE]
> Tento konektor je v [ *ve verzi public preview*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

S Azure Logic Apps a konektor IBM 3270 můžete přístup a spustit IBM mainframových aplikací, obvykle ovládající tak, že přejdete prostřednictvím 3270 emulátor obrazovky. Tímto způsobem IBM mainframových aplikací můžete integrovat s Azure, Microsoft a další aplikace, služby a systémy tak, že vytvoříte automatizované pracovní postupy s Azure Logic Apps. Konektor s sálové počítače IBM komunikuje pomocí protokolu TN3270 a je k dispozici ve všech oblastech Azure Logic Apps s výjimkou Azure Government a Azure China 21Vianet. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Tento článek popisuje tyto aspekty pro použití konektoru 3270: 

* Proč používat konektor IBM 3270 v Azure Logic Apps a vykonávání 3270 aplikace řízené modelem obrazovky konektoru

* Požadavky a nastavení pro použití konektoru 3270

* Postup přidání akce 3270 konektor do aplikace logiky

## <a name="why-use-this-connector"></a>Proč používat tento konektor?

Pro přístup k aplikacím na sálové počítače IBM, obvykle použijete 3270 emulaci terminálu, často označované jako "zelené obrazovka". Tato metoda je nabízí způsob, ale má omezení. I když nemusí být možné Host Integration serveru (HIS) pomáhá při práci přímo s těmito aplikacemi, v některých případech oddělení obrazovky a obchodní logiky. Nebo možná už máte informace o fungování hostování aplikací.

Pro rozšíření scénářů, konektor IBM 3270 v Azure Logic Apps pracuje s 3270 návrhářský nástroj, který použijete k záznamu, nebo "zachytávání", hostitel obrazovky používané pro specifické úlohy, definují tok navigace pro tuto úlohu prostřednictvím vaší aplikace mainframových a definovat metody s vstupní a výstupní parametry pro tuto úlohu. Návrhářský nástroj převádí metadata, která při volání akce, která představuje tuto úlohu z aplikace logiky používá konektor 3270 těchto informací.

Po vytvoření souboru metadat z nástroje pro návrh, přidejte tento soubor do účtu pro integraci v Azure. Tímto způsobem, aplikace logiky můžete získat přístup k vaší aplikace metadatům při přidání akce konektor 3270. Konektor přečte soubor metadat z účtu pro integraci zpracovává navigaci 3270 obrazovek a dynamicky uvede parametry pro akce 3270 konektoru. Potom můžete zadat data pro hostitelskou aplikaci a konektor vrátí výsledky do aplikace logiky. Tímto způsobem můžete integrovat starší verze aplikací s Azure, Microsoft a další aplikace, služby a systémy, které podporuje Azure Logic Apps.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Doporučené: [Prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Vyberte toto prostředí jako umístění pro vytvoření a spuštění aplikace logiky. ISE zajišťuje přístup z aplikace logiky k prostředkům, které jsou chráněné uvnitř virtuální sítě Azure.

* Aplikace logiky pro automatizaci a vaší aplikaci řízenou obrazovky 3270

  Konektor IBM 3270 nemá aktivačních událostí, proto použít jinou aktivační událost pro spuštění aplikace logiky, jako **opakování** aktivační události. Poté můžete přidat akce 3270 konektorů. Abyste mohli začít, [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Pokud používáte ISE, vyberte tento ISE jako umístění aplikace logiky.

* [Stáhněte a nainstalujte návrhářský nástroj 3270](https://aka.ms/3270-design-tool-download).
Jediným předpokladem je [rozhraní Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Tento nástroj pomůže záznam obrazovky, navigačních cesty, metody a parametry pro úkoly v aplikaci pro přidávání a spustit jako 3270 akce konektorů. Nástroj vygeneruje soubor hostitele Integration návrháře XML (HIDX), který poskytuje nezbytných metadat pro konektor nekonfigurovali používání za zvyšování mainframových aplikací.
  
  Po stažení a instalaci tohoto nástroje, postupujte podle těchto kroků pro připojení k hostiteli:

  1. Otevřete nástroj 3270 návrhu. Z **relace** nabídce vyberte možnost **hostitele relace**.
  
  1. Zadejte hostitele TN3270 informace o serveru.

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), což je místo, které budete ukládat svůj soubor HIDX jako mapování tak, aby vaše aplikace logiky může přístup definice metadat a metoda v tomto souboru. 

  Ujistěte se, že váš účet pro integraci je propojený na aplikaci logiky, které používáte. Navíc pokud použijete ISE, ujistěte se, že umístění účtu integrace je stejný ISE, který používá vaše aplikace logiky.

* Přístup k TN3270 serveru, který je hostitelem aplikace sálového počítače

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Vytvoření přehled metadat

V 3270 řízené obrazovky aplikace obrazovky a datová pole jsou jedinečné pro vaše scénáře, takže konektoru 3270 musí tyto informace o aplikaci, která můžete zadat jako metadata. Tato metadata popisují informace, které pomáhá identifikovat a rozpoznat obrazovky aplikace logiky, popisuje, jak navigace mezi obrazovkami, kam se vstupní data a kde mají očekávat výsledky. K určení a generovat tato metadata, použijete nástroj 3270 návrh, který vás provede tyto specifické *režimy*, nebo fáze, jak je popsáno dále v další podrobnosti:

* **Zachycení**: V tomto režimu záznamu obrazovky potřebné k dokončení konkrétních úkolů s mainframových aplikací, například získání Bankovní zůstatek.

* **Navigace**: V tomto režimu je třeba zadat plán nebo cestu k tom, jak procházet vaše mainframových aplikací obrazovky pro konkrétní úlohu.

* **metody**: V tomto režimu můžete například definovat metodu, `GetBalance`, popisující obrazovky navigační cestu. Můžete také zvolit pole na každou obrazovku, která označuje jako metody vstupní a výstupní parametry.

### <a name="unsupported-elements"></a>Nepodporované elementy

Návrhářský nástroj nepodporuje tyto prvky:

* Mapy částečná podpora základní mapování na IBM (MS): Při importu mapy MS, ignoruje návrhářský nástroj definice částečné obrazovky.
* V vstupně-výstupní parametry: Nelze definovat parametry In nebo Out.
* Zpracování nabídky: Není podporováno ve verzi preview
* Zpracování pole: Není podporováno ve verzi preview

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Zachycení obrazovky

V tomto režimu se označit položku na každý 3270 obrazovky, který jednoznačně identifikuje této obrazovce. Můžete například určit řádek textu nebo složitější sadu podmínek, jako je například určitý text a prázdné pole. Můžete zaznamenat tyto obrazovky přes živé připojení k serveru hostitele, nebo importovat tyto informace z mapování podporu základní mapování na IBM (MS). Živé připojení používá TN3270 emulátor pro připojení k hostiteli. Každý konektor akce musí být namapovaný na jednu úlohu, která začíná s připojením k relaci a končí odpojení z relace.

1. Pokud jste to ještě neudělali, otevřete 3270 návrhářský nástroj. Na panelu nástrojů zvolte **zachycení** tak, že zadáte režim zachycení.

1. Záznam spustíte stisknutím klávesy F5 nebo **záznam** nabídce vyberte možnost **spustit záznam**. 

1. Z **relace** nabídce vyberte možnost **připojit**.

1. V **zachycení** podokno, počínaje první obrazovky ve vaší aplikaci, krokovat aplikaci pro konkrétní úlohu, která nahráváte.

1. Po dokončení úlohy se odhlaste z aplikace jako obvykle.

1. Z **relace** nabídce vyberte možnost **odpojit**.

1. Chcete-li ukončit nahrávání, stiskněte klávesy Shift + F5 klíče, nebo z **záznam** nabídce vyberte možnost **zastavit záznam**.

   Po zachycení obrazovky pro úlohu, zobrazí nástroj Návrhář miniatury, které představují těchto obrazovek. Několik poznámek o tyto miniatury:

   * Součástí zachycené obrazovek, máte obrazovky s názvem "Prázdný".

     Když se poprvé připojíte ke [CICS](https://www.ibm.com/it-infrastructure/z/cics), musíte poslat klíči "Clear", než budete moct zadat název transakce, kterou chcete spustit. Obrazovka, kam poslat klíči "Clear" neobsahovalo žádné *rozpoznávání atributy*, jako je například název obrazovky, které můžete přidat pomocí editoru obrazovky rozpoznávání. K reprezentaci této obrazovce, zahrnuje miniatury obrazovky s názvem "Prázdný". Později můžete tuto obrazovku představující obrazovky ve kterém zadáte název transakce.

   * Ve výchozím nastavení používá název pro zachycené obrazovku první slovo na obrazovce. Pokud tento název už existuje, připojí návrhářský nástroj název symbol podtržítka a číslo, například "WBGB" a "WBGB_1".

1. Zachycené obrazovky poskytnout smysluplnějšího názvu, postupujte podle těchto kroků:

   1. V **hostitele obrazovky** podokně, vyberte obrazovku, kterou chcete přejmenovat.

   1. V podokně stejné dole v podokně stejné najít **název obrazovky** vlastnost.

   1. Změňte název aktuální obrazovky do více popisný název.

1. Teď zadejte pole k identifikaci jednotlivých obrazovek.

   S 3270 datový proud obrazovky nemají výchozí identifikátory, proto je nutné vybrat jedinečný text na jednotlivých obrazovkách. Pro komplexní scénáře můžete zadat více podmínek, například jedinečný text a pole s určitou podmínku.

Po dokončení výběru polí rozpoznávání přesuňte do dalšího režimu.

### <a name="conditions-for-identifying-repeated-screens"></a>Podmínky pro identifikaci opakované obrazovky

Konektor k navigaci a pro rozlišení obrazovky obvykle najít jedinečnou text na obrazovku, která můžete použít jako identifikátor mezi zachycené obrazovky. Pro opakované obrazovek může být nutné další metody identifikace. Předpokládejme například, že máte dvě obrazovky, které vypadají stejně, s výjimkou jednu obrazovku vrátí platnou hodnotu, zatímco jiné obrazovky vrátí chybovou zprávu.

V nástroji návrhu můžete přidat *rozpoznávání atributy*, například název obrazovky jako je například "Získat zůstatek na účtu", pomocí editoru obrazovky rozpoznávání. Pokud máte rozvětvené cesty a vrácení obou větví na stejné obrazovce, ale s různé výsledky, budete potřebovat další atributy rozpoznávání. V době běhu konektor používá tyto atributy pro určení aktuální větev a větve. Tady jsou podmínky, které můžete použít:

* Konkrétní hodnota: Tato hodnota odpovídá zadaného řetězce v zadaném umístění.
* Konkrétní hodnota: Tato hodnota se neshoduje zadaného řetězce v zadaném umístění.
* Prázdný: Toto pole je prázdné.
* Obsahuje hodnoty: Toto pole není prázdné.

Další informace najdete v tématu [příklad navigace plán](#example-plan) dále v tomto tématu.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definujte plány navigace

V tomto režimu definujete, toku nebo kroky pro navigaci v aplikaci mainframových obrazovky pro konkrétní úlohu. Například v některých případech může mít více než jednu cestu, která vaše aplikace může trvat, kde jedna cesta vytvoří správný výsledek, zatímco jiné cesty dojde k chybě. Pro každou obrazovku, určete nezbytné pro přechod na další obrazovce například stisknutí kláves `CICSPROD <enter>`.

> [!TIP]
> Pokud používáte automatizaci několik úloh, které používají stejné připojení a odpojení obrazovek, návrhářský nástroj poskytuje zvláštní typy schémat připojit a odpojit. Při definování tyto plány, přidáte je do plánu navigace začátek a konec.

### <a name="guidelines-for-plan-definitions"></a>Pokyny pro definice plánu

* Zahrnout všechny obrazovky, počínaje připojení a konče odpojení.

* Můžete vytvořit samostatný plán nebo použít připojit a odpojit plány, které umožňují opakované použití řadou obrazovek, které jsou společné pro všechny transakce.

  * Poslední obrazovka v plánu připojit musí být na stejné obrazovce jako první obrazovky ve vašem plánu navigace.

  * První obrazovka v plánu odpojit musí být stejné obrazovce jako poslední obrazovky ve vašem plánu navigace.

* Zachycené obrazovky může obsahují mnoho opakovaných obrazovky, proto vyberte a použijte pouze jednu instanci libovolné opakované obrazovky ve vašem plánu. Tady je několik příkladů opakované obrazovky:

  * Přihlašovací obrazovka, například **MSG 10** obrazovky
  * Uvítací obrazovce CICS
  * "Vymazat" nebo **prázdný** obrazovky

<a name="create-plans"></a>

### <a name="create-plans"></a>Vytváření plánů

1. Na panelu nástrojů 3270 návrhářský nástroj, zvolte **navigace** tak, že zadáte navigační režim.

1. Ve spuštění vašeho plánu **navigace** podokně zvolte **nový plán**.

1. V části **zvolte nový název plánu**, zadejte název pro váš plán. Z **typ** vyberte typ plánu:

   | Typ plánu | Popis |
   |-----------|-------------|
   | **Proces** | Pro samostatnou službu nebo kombinované plány |
   | **Připojení** | Pro plány Connect |
   | **Odpojení** | Pro odpojení plány |
   |||

1. Z **hostitele obrazovky** podokno, přetáhněte zachycené miniatury k plánu navigace plochu v **navigace** podokně.

   Chcete-li představují prázdnou obrazovku, kde zadejte název transakce, použijte "Prázdný" obrazovku.

1. Uspořádejte obrazovek v pořadí, ve kterém popisuje úlohy, které definujete.

1. Chcete-li definovat cesty toku mezi obrazovkami, včetně rozvětvení a spojení na návrhářský nástroj nástrojů, zvolte **toku**.

1. Vyberte první obrazovku v toku. Přetáhněte a vykreslení připojení na další obrazovce v toku.

1. Pro každou obrazovku, zadejte hodnoty pro **klíč podpory** vlastnosti (pozornost Identifier) a **pevný Text** vlastnost, která tok přesune na další obrazovce.

   Můžete mít jenom klíč podpory nebo podporu klíč i pevného textu.

Po dokončení plánu navigace, můžete [definovat metody v režimu Další](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Příklad:

V tomto příkladu předpokládejme, že spustíte CICS transakci s názvem "WBGB", který obsahuje tyto kroky: 

* Na první obrazovce zadejte název a účet.
* Na druhé obrazovce získáte zůstatek na účtu.
* Ukončení "Prázdný" obrazovku.
* Odhlášení od CICS na obrazovku "MSG-10".

Předpokládejme také, že tento postup opakujte, ale zadejte nesprávná data tak lze zachytit obrazovku, která zobrazí chybu. Tady jsou obrazovky, které zaznamenáte:

* MSG-10
* Vítejte CICS
* Prázdné
* WBGB_1 (vstup)
* WBGB_2 (chyba)
* Empty_1
* MSG-10_1

Některé obrazovky se stejnou obrazovku, například "MSG-10" a "Prázdný" to ale jedinečné názvy mnoha obrazovkách tady získáte. Pro opakované obrazovku, pouze jedna instance používala pro tuto obrazovku v plánu. Tady jsou příklady, které ukazují, jak může vypadat samostatný plán, připojit plánu, plán odpojit a kombinované plán:

* Samostatný plán

  ![Samostatný plán navigace](./media/connectors-create-api-3270/standalone-plan.png)

* Připojení plánu

  ![Připojení plánu](./media/connectors-create-api-3270/connect-plan.png)

* Odpojit plán

  ![Odpojit plán](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinované plán

  ![Kombinované plán](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Příklad: Identifikujte opakované obrazovky

Konektor k navigaci a pro rozlišení obrazovky obvykle najít jedinečný text na obrazovku, která můžete použít jako identifikátor na zachycené obrazovkách. Pro opakované obrazovek může být nutné další metody identifikace. Příklad plánu má fork, kde můžete získat obrazovek, které se podobají. Jednu obrazovku vrátí zůstatek na účtu, zatímco jiné obrazovky vrátí chybovou zprávu.

Návrhářský nástroj umožňuje přidat rozpoznávání atributy, například název obrazovky s názvem "Get zůstatek na účtu", pomocí rozpoznávání obrazovky editoru. V tomto případě podobné obrazovky budete potřebovat další atributy. V době běhu konektor používá tyto atributy pro určení větev a větve.

* Ve větvi, která vrací platný vstup, který je na obrazovce s zůstatek na účtu, můžete přidat pole, která má podmínku "neprázdné".

* Ve větvi, která vrátí chybu můžete přidat pole, která má podmínku "prázdný".

<a name="define-method"></a>

## <a name="define-methods"></a>Definování metody

V tomto režimu definujete metodu, která je přidružená k plánu navigace. Pro každý parametr metody zadejte datový typ, jako je řetězec, celé číslo, datum nebo čas a tak dále. Jakmile budete hotovi, můžete otestovat metodu na hostiteli za provozu a potvrďte, že metoda funguje podle očekávání. Potom generovat soubor metadat nebo Host Integration návrháře XML (HIDX) soubor, který má teď definice metod pro vytváření a spouštění akce pro konektor IBM 3270.

1. Na panelu nástrojů 3270 návrhářský nástroj, zvolte **metody** tak, aby metody režimu. 

1. V **navigace** podokně, vyberte obrazovku, která má vstupních polí má.

1. Chcete-li přidat první vstupního parametru pro metodu, postupujte takto:

   1. V **zachycení** podokně na obrazovce emulátoru 3270 zvolte celé pole, nikoli pouze text v poli, který má jako první vstup.

      > [!TIP]
      > Zobrazit všechna pole a ujistěte se, že vyberete pole, na **zobrazení** nabídce vyberte možnost **všechna pole**.

   1. Na panelu nástrojů návrhářský nástroj, zvolte **vstupní pole**. 

   Chcete-li přidat více vstupními parametry, opakujte předchozí kroky pro každý parametr.

1. Chcete-li přidat první výstupního parametru pro metodu, postupujte takto:

   1. V **zachycení** podokně na obrazovce emulátoru 3270 zvolte celé pole, nikoli pouze text v poli, který má jako první výstup.

      > [!TIP]
      > Zobrazit všechna pole a ujistěte se, že vyberete pole, na **zobrazení** nabídce vyberte možnost **všechna pole**.

   1. Na panelu nástrojů návrhářský nástroj, zvolte **výstupního pole pro**.

   Chcete-li přidat další výstupní parametry, opakujte předchozí kroky pro každý parametr.

1. Po přidání všech vašich parametrů metody definují tyto vlastnosti pro každý parametr:

   | Název vlastnosti | Možné hodnoty | 
   |---------------|-----------------|
   | **Datový typ** | Byte, datum a čas, Decimal, Int, Long, Short, řetězce |
   | **Metoda výplň pole** | Parametry podporují tyto typy výplně vyplnění pomocí prázdných hodnot v případě potřeby: <p><p>- **Typ**: Zadejte postupně znaků do pole. <p>- **Vyplnit**: Nahraďte obsah pole znaků vyplnění pomocí prázdných hodnot v případě potřeby. <p>- **EraseEofType**: Zrušte zaškrtnutí políčka a pak zadejte postupně do pole znaků. |
   | **Formátovací řetězec** | Některé datové typy parametrů použijte řetězec formátu, která informuje o konektoru 3270 jak převést text z obrazovky na datový typ .NET: <p><p>- **Datum a čas**: Následuje řetězec formátu data a času [řetězce formátu .NET vlastní data a času](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Například datum `06/30/2019` používá řetězec formátu `MM/dd/yyyy`. <p>- **Desetinné**: Řetězec formátu desítkové soustavy používá [COBOL obrázek klauzule](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Například číslo `100.35` používá řetězec formátu `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Uložit a zobrazit metadata

Potom co definujete metodu, ale před testováním metodu, uložte všechny informace, které jste definovali, zatím k souboru pro autorizaci prostředků (.rap).
Kdykoli během libovolném jiném režimu, můžete uložit do tohoto souboru pro autorizaci prostředků. Návrhářský nástroj také zahrnuje ukázkový soubor pro autorizaci prostředků, které můžete otevřít a zkontrolovat tak, že přejdete do instalační složky návrhářský nástroj na tomto místě a otevírání souboru "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Pokud se pokusíte ukládání změn do ukázkového souboru pro autorizaci prostředků nebo generování souboru HIDX z ukázkového souboru pro autorizaci prostředků, zatímco soubor zůstane v instalační složce Nástroje návrhu, ale pravděpodobně dojde k chybě "přístup byl odepřen". Ve výchozím nastavení nainstaluje nástroj návrhu ve složce Program Files bez zvýšenou úroveň oprávnění. Pokud dojde k chybě, zkuste použijte jeden z těchto řešení:

* Ukázkový soubor zkopírujte do jiného umístění.
* Návrhářský nástroj spusťte jako správce.
* Stát vlastníkem složky sady SDK.

## <a name="test-your-method"></a>Testovací metodu

1. Tím spustíte metodu živé hostitele, zatímco jste pořád v režimu metody, stiskněte klávesu F5 nebo z návrhářský nástroj nástrojů, zvolte **spustit**.

   > [!TIP]
   > Režimy můžete kdykoli změnit. Na **souboru** nabídce vyberte možnost **režimu**a potom vyberte požadovaný režim.

1. Zadejte hodnoty parametrů a zvolte **OK**.

1. Chcete-li pokračovat na další obrazovku, zvolte **Další**.

1. Až budete hotovi, zvolte **provádí**, které ukazuje všechny hodnoty výstupních parametrů.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generování a nahrávání souboru HIDX

Jakmile budete připraveni, vygenerujte soubor HIDX tak můžete nahrát do účtu pro integraci. Návrhářský nástroj 3270 vytvoří soubor HIDX v novou podsložku, kam jste uložili soubor pro autorizaci prostředků.

1. Na panelu nástrojů 3270 návrhářský nástroj, zvolte **generovat kód**.

1. Přejděte do složky, která obsahuje váš soubor pro autorizaci prostředků a otevřete podsložku, kterou vytvořili nástroj po vygenerování souboru HIDX. Ověřte, že nástroj vytvoření souboru HIDX.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a vyhledejte svůj účet integrace.

1. Přidat váš soubor HIDX jako mapa k účtu pro integraci pomocí [tyto podobným způsobem pro přidání mapy](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ale když vyberete typ mapy, vyberte **HIDX**.

Dále v tomto tématu přidáte akci IBM 3270 do aplikace logiky poprvé, budete vyzváni k vytvoření připojení mezi vaší aplikace logiky a server hostitele tím, že poskytuje informace o připojení, jako jsou názvy pro integrační účet a hostitelů serveru . Po vytvoření připojení můžete vyberte dříve přidaný soubor HIDX způsob spuštění a parametry se mají použít.

Po dokončení těchto kroků, pomocí akce, kterou vytvoříte ve vaší aplikaci logiky pro připojení k IBM mainframových, jednotky obrazovky pro vaši aplikaci, zadejte data, vrátí výsledky a podobně. Také můžete pokračovat v přidávání dalších akcí do aplikace logiky pro integraci s jinými aplikací, služeb a systémů.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Spustit akce IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**a vyberte **přidat akci**. 

1. Pod vyhledávacím polem vyberte **Enterprise**. Do vyhledávacího pole zadejte jako filtr "3270". Ze seznamu akcí vyberte tuto akci: **Spustí program sálové počítače přes připojení TN3270**

   ![Vyberte akci 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Pokud ještě neexistuje žádné připojení, zadejte informace potřebné pro připojení a zvolte **vytvořit**.

   | Vlastnost | Požaduje se | Value | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | <*Název připojení*> | Název připojení |
   | **ID účtu pro integraci** | Ano | <*integration-account-name*> | Název účtu integrace |
   | **Adresa URL SAS účtu integrace** | Ano | <*integration-account-SAS-URL*> | Účet integrace sdíleného přístupového podpisu (SAS) adresu URL, která můžete vygenerovat z nastavení vašeho účtu integrace na webu Azure Portal. <p>1. Na vaše integrační účet nabídce v části **nastavení**vyberte **adresu URL zpětného volání**. <br>2. V pravém podokně zkopírujte **vygeneruje adresu URL zpětného volání** hodnotu. |
   | **Server** | Ano | <*TN3270-server-name*> | Název serveru pro vaši službu TN3270 |
   | **Port** | Ne | <*TN3270-server-port*> | Port používaný serverem služby TN3270. Pokud je ponecháno prázdné, konektor používá `23` jako výchozí hodnotu. |
   | **Typ zařízení** | Ne | <*IBM-terminal-model*> | Název modelu nebo číslo pro Terminálové IBM k emulaci. Pokud je ponecháno prázdné, konektor používá výchozí hodnoty. |
   | **Znaková stránka** | Ne | <*code-page-number*> | Číslo stránky kód pro hostitele. Pokud je ponecháno prázdné, konektor používá `37` jako výchozí hodnotu. |
   | **Název logické jednotky** | Ne | <*Logický název jednotky*> | Název konkrétní logické jednotky k vyžádání z hostitele |
   | **Povolit SSL?** | Ne | Zapnutí nebo vypnutí | Zapnutí nebo vypnutí šifrování SSL. |
   | **Ověření certifikátu ssl hostitele?** | Ne | Zapnutí nebo vypnutí | Zapnout nebo vypnout ověřování certifikátu serveru. |
   ||||

   Příklad:

   ![Vlastnosti připojení](./media/connectors-create-api-3270/connection-properties.png)

1. Zadejte informace potřebné pro akce:

   | Vlastnost | Požaduje se | Value | Popis |
   |----------|----------|-------|-------------|
   | **Název Hidx** | Ano | <*HIDX-file-name*> | Vyberte soubor HIDX 3270, který chcete použít. |
   | **Název metody** | Ano | <*Název metody*> | Vyberte metodu v HIDX souboru, který chcete použít. Po výběru metody, **přidat nový parametr** seznamu se zobrazí, takže můžete vybrat parametry se mají použít s danou metodu. |
   ||||

   Příklad:

   **Vyberte soubor HIDX**

   ![Vyberte soubor HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Vyberte metodu**

   ![Vybrat metodu](./media/connectors-create-api-3270/select-method.png)

   **Vyberte parametry**

   ![Vyberte parametry](./media/connectors-create-api-3270/add-parameters.png)

1. Jakmile budete hotovi, uložte a spusťte aplikaci logiky.

   Po svoji logiku aplikace dokončí spuštěné, kroky z běhu jsou zobrazeny. 
   Úspěšný postup značky zaškrtnutí při neúspěšné kroky ukazují písmeno "X".

1. Zkontrolujte vstupy a výstupy jednotlivých kroků, rozbalte položku tohoto kroku.

1. Chcete-li zkontrolovat výstupy, zvolte **zobrazit nezpracované výstupy**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o limitu, akce a aktivačních událostí, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, zkontrolujte [konektoru referenční stránce](/connectors/si3270/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
