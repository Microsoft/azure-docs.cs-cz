---
title: Připojení k 3270 aplikacím v sálových počítačích IBM pomocí Azure-Azure Logic Apps
description: Integrace a automatizace 3270 aplikací řízených obrazovkou pomocí Azure pomocí Azure Logic Apps a konektoru IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 50b8fc6b6a350d0a5982cc84f94067979d018cce
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050671"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrace 3270 aplikací řízených obrazovkou na sálové počítače IBM s Azure pomocí Azure Logic Apps a konektoru IBM 3270

> [!NOTE]
> Tento konektor je ve [*verzi Public Preview*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Pomocí Azure Logic Apps a konektoru IBM 3270 můžete používat a spouštět aplikace IBM sálové sami, které obvykle provedete tak, že přejdete na obrazovky emulátoru 3270. Tímto způsobem můžete své aplikace IBM pro sálové počítače integrovat s Azure, Microsoftem a dalšími aplikacemi, službami a systémy tím, že vytvoříte automatizované pracovní postupy s Azure Logic Apps. Konektor komunikuje s sálovými počítače IBM pomocí protokolu TN3270 a je k dispozici ve všech oblastech Azure Logic Apps s výjimkou Azure Government a Azure Čína 21Vianet. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Tento článek popisuje tyto aspekty použití konektoru 3270: 

* Důvody použití konektoru IBM 3270 v Azure Logic Apps a způsobu, jakým konektor spouští 3270 aplikace řízené obrazovkami

* Požadavky a nastavení pro použití konektoru 3270

* Postup přidání akcí konektoru 3270 do aplikace logiky

## <a name="why-use-this-connector"></a>Proč používat tento konektor?

Pro přístup k aplikacím na sálových počítačích IBM obvykle používáte emulátor terminálu 3270, který se často nazývá "zelená obrazovka". Tato metoda je časově testována, ale má omezení. I když Host Integration Server (jeho) pomáhá pracovat přímo s těmito aplikacemi, někdy nemusí být oddělení obrazovky a obchodní logiky možné. Nebo možná nebudete již mít informace o fungování hostitelských aplikací.

Aby se tyto scénáře rozšířily, konektor IBM 3270 v Azure Logic Apps funguje s nástrojem pro návrh 3270, který použijete k záznamu nebo zachycení, na hostitelských obrazovkách použitých pro konkrétní úlohu, k definování toku navigace pro daný úkol v rámci aplikace v rámci sálového počítače a definování metody se vstupními a výstupními parametry pro daný úkol. Nástroj pro návrh převede tyto informace na metadata, která konektor 3270 používá při volání akce, která představuje tuto úlohu z vaší aplikace logiky.

Po vygenerování souboru metadat z nástroje pro návrh můžete tento soubor přidat do účtu pro integraci v Azure. Aplikace logiky tak bude mít přístup k metadatům vaší aplikace, když přidáte akci konektoru 3270. Tento konektor přečte soubor metadat z účtu integrace, zpracovává navigaci přes obrazovky 3270 a dynamicky prezentuje parametry pro akci konektoru 3270. Potom můžete poskytnout data do hostitelské aplikace a konektor vrátí výsledky do vaší aplikace logiky. Tímto způsobem můžete své starší aplikace integrovat s Azure, Microsoftem a dalšími aplikacemi, službami a systémy, které Azure Logic Apps podporuje.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Doporučené: [Prostředí služby Integration Service (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Toto prostředí můžete vybrat jako umístění pro vytvoření a spuštění aplikace logiky. ISE poskytuje přístup z vaší aplikace logiky k prostředkům chráněným v rámci virtuálních sítí Azure.

* Aplikace logiky, která se má použít pro automatizaci a spuštění aplikace řízené obrazovkou 3270

  Konektor IBM 3270 nemá triggery, proto použijte jiný Trigger ke spuštění aplikace logiky, jako je například Trigger **opakování** . Pak můžete přidat akce konektoru 3270. Začněte tím, [že vytvoříte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Pokud používáte ISE, vyberte tuto ISE jako umístění vaší aplikace logiky.

* [Stáhněte a nainstalujte nástroj pro návrh 3270](https://aka.ms/3270-design-tool-download).
Jediným předpokladem je [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Tento nástroj vám pomůže nahrávat obrazovky, cesty navigace, metody a parametry pro úlohy ve vaší aplikaci, které přidáte a spouštíte jako akce konektoru 3270. Nástroj vygeneruje soubor XML návrháře integrace (HIDX) hostitele, který poskytuje nezbytná metadata pro konektor, který se má použít k řízení vaší sálové aplikace.
  
  Po stažení a instalaci tohoto nástroje postupujte podle těchto kroků a připojte se k hostiteli:

  1. Otevřete nástroj pro návrh 3270. V nabídce **relace** vyberte možnost **hostitelové relace**.
  
  1. Zadejte informace o hostitelském serveru TN3270.

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), což je místo, kam uložíte soubor HIDX jako mapu, takže vaše aplikace logiky má přístup k metadatům a definicím metod v tomto souboru. 

  Ujistěte se, že je váš účet pro integraci propojený s aplikací logiky, kterou používáte. Pokud používáte ISE, ujistěte se, že je umístění účtu pro integraci stejné ISE, jakou používá aplikace logiky.

* Přístup k serveru TN3270, který je hostitelem vaší sálové aplikace

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Přehled vytvoření metadat

V aplikaci řízené obrazovkou 3270 jsou obrazovky a datová pole pro vaše scénáře jedinečné, takže konektor 3270 potřebuje tyto informace o vaší aplikaci, které můžete zadat jako metadata. Tato metadata popisují informace, které pomáhají vaší aplikaci logiky identifikovat a rozpoznávat obrazovky, popisuje, jak přecházet mezi obrazovkami, kde můžete zadávat data a kde očekávat výsledky. Chcete-li zadat a vygenerovat tato metadata, použijte nástroj pro návrh 3270, který vás provede těmito konkrétními *režimy*nebo fázemi, jak je popsáno dále ve více podrobnostech:

* **Zachytit**: V tomto režimu si zaznamenáte obrazovky, které jsou potřeba k dokončení konkrétní úlohy, pomocí své aplikace v rámci sálového počítače, například získání zůstatku bank.

* **Navigace**: V tomto režimu určíte plán nebo cestu, jak procházet obrazovky vaší sálové aplikace pro konkrétní úkol.

* **Metody**: V tomto režimu definujete metodu, například `GetBalance`, která popisuje cestu k navigaci obrazovky. Můžete také zvolit pole na každé obrazovce, která se stanou vstupními a výstupními parametry metody.

### <a name="unsupported-elements"></a>Nepodporované elementy

Nástroj pro návrh nepodporuje tyto prvky:

* Částečná mapování BMS (IBM Basic Mapping support): Pokud importujete mapu BMS, nástroj pro návrh ignoruje definice částečné obrazovky.
* Vstupně-výstupní parametry: Nemůžete definovat vstupně-výstupní parametry.
* Zpracování nabídky: Nepodporováno během verze Preview
* Zpracování pole: Nepodporováno během verze Preview

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Zachytit obrazovky

V tomto režimu označíte položku na všech obrazovkách 3270, které tuto obrazovku jedinečně identifikují. Můžete například zadat řádek textu nebo složitější sadu podmínek, například konkrétní text a neprázdné pole. Tyto obrazovky můžete nahrát přes živé připojení k hostitelskému serveru, nebo tyto informace naimportovat z mapy podpory BMS (IBM Basic Mapping support). Živé připojení používá emulátor TN3270 pro připojení k hostiteli. Každá akce konektoru musí být namapována na jeden úkol, který začíná připojením k vaší relaci a končí odpojením od relace.

1. Pokud jste to ještě neudělali, otevřete nástroj pro návrh 3270. Na panelu nástrojů vyberte možnost **zachytit** , abyste zadávali režim zachytávání.

1. Chcete-li spustit záznam, stiskněte klávesu F5 nebo v nabídce **záznamu** vyberte možnost **Spustit záznam**. 

1. V nabídce **relace** vyberte **připojit**.

1. V podokně **zachycení** začněte v aplikaci pro konkrétní úlohu, kterou právě zaznamenáte, na první obrazovce.

1. Po dokončení úlohy se z aplikace odhlaste obvyklým způsobem.

1. V nabídce **relace** vyberte **Odpojit**.

1. Pokud chcete zastavit nahrávání, stiskněte klávesy Shift + F5 nebo v nabídce **nahrávání** vyberte **zastavit nahrávání**.

   Po zachycení obrazovek pro úlohu zobrazí nástroj Návrhář miniatury, které tyto obrazovky reprezentují. Některé poznámky k těmto miniaturám:

   * Máte obrazovku s názvem "Empty", která je součástí vašich zachycených obrazovek.

     Když se poprvé připojíte k [CICS](https://www.ibm.com/it-infrastructure/z/cics), je nutné před zadáním názvu transakce, kterou chcete spustit, odeslat klíč "Clear". Obrazovka, na které jste odeslali klíč "Clear", nemá žádné *atributy rozpoznávání*, jako je například název obrazovky, který můžete přidat pomocí editoru pro rozpoznávání obrazovky. Pro reprezentaci této obrazovky miniatury obsahuje obrazovku s názvem "Empty". Tuto obrazovku můžete později použít k reprezentaci obrazovky, na které jste zadali název transakce.

   * Ve výchozím nastavení používá název zaznamenané obrazovky první slovo na obrazovce. Pokud tento název již existuje, nástroj pro návrh připojí název podtržítkem a číslem, například "WBGB" a "WBGB_1".

1. K získání smysluplného názvu pro zaznamenanou obrazovku použijte následující postup:

   1. V podokně **hostitelské obrazovky** vyberte obrazovku, kterou chcete přejmenovat.

   1. Ve stejném podokně poblíž dolního okraje ve stejném podokně najděte vlastnost **Název obrazovky** .

   1. Změňte název aktuální obrazovky na výstižnější název.

1. Nyní zadejte pole pro identifikaci každé obrazovky.

   S datovým proudem 3270 mají obrazovky výchozí identifikátory, takže musíte na každé obrazovce vybrat jedinečný text. U složitých scénářů můžete zadat více podmínek, například jedinečný text a pole s konkrétní podmínkou.

Po dokončení výběru polí rozpoznávání přejděte k dalšímu režimu.

### <a name="conditions-for-identifying-repeated-screens"></a>Podmínky identifikace opakujících se obrazovek

Aby konektor mohl procházet a rozlišovat mezi obrazovkami, obvykle se na obrazovce nachází jedinečný text, který můžete použít jako identifikátor mezi zachycenými obrazovkami. Pro opakované obrazovky možná budete potřebovat více metod identifikace. Předpokládejme například, že máte dvě obrazovky, které vypadají stejně, kromě jedné obrazovky, vrátí platnou hodnotu, zatímco druhá obrazovka vrátí chybovou zprávu.

V nástroji pro návrh můžete přidat *atributy rozpoznávání*, například název obrazovky, jako je například "získání zůstatku účtu", pomocí editoru rozpoznávání obrazovky. Pokud máte rozvětvenou cestu a obě větve vrací stejnou obrazovku, ale s různými výsledky, budete potřebovat další atributy rozpoznávání. V době běhu konektor používá tyto atributy k určení aktuální větve a rozvětvení. Tady jsou podmínky, které můžete použít:

* Konkrétní hodnota: Tato hodnota odpovídá zadanému řetězci v zadaném umístění.
* Nejedná se o konkrétní hodnotu: Tato hodnota se neshoduje se zadaným řetězcem v zadaném umístění.
* Obsahovat Toto pole je prázdné.
* Neprázdné: Toto pole není prázdné.

Další informace najdete v [příkladu navigační plán](#example-plan) dále v tomto tématu.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definování plánů navigace

V tomto režimu definujete tok nebo kroky pro navigaci na obrazovkách vaší aplikace pro konkrétní úlohu. Například může být třeba mít více než jednu cestu, kterou může aplikace převzít, když jedna cesta vytvoří správný výsledek, zatímco druhá cesta vytvoří chybu. Pro každou obrazovku určete stisknutí kláves nutných pro přechod na další obrazovku, `CICSPROD <enter>`jako je například.

> [!TIP]
> Pokud automatizujete několik úloh, které používají stejné obrazovky připojení a odpojení, poskytuje nástroj pro návrh speciální typy plánů připojení a odpojení. Při definování těchto plánů je můžete přidat do začátku a koncem svého navigačního plánu.

### <a name="guidelines-for-plan-definitions"></a>Pokyny pro definice plánů

* Zahrňte všechny obrazovky počínaje připojením a ukončením odpojení.

* Můžete vytvořit samostatný plán nebo použít plány připojení a odpojení, které vám umožní znovu použít řadu obrazovek společných pro všechny vaše transakce.

  * Poslední obrazovka v plánu připojení musí být stejná jako první obrazovka v plánu navigace.

  * První obrazovka v plánu odpojení musí být stejná jako poslední obrazovka v plánu navigace.

* Vaše zachycené obrazovky mohou obsahovat mnoho opakovaných obrazovek, takže vyberte a používejte pouze jednu instanci všech opakovaných obrazovek v plánu. Tady je několik příkladů opakujících se obrazovek:

  * Přihlašovací obrazovka, například obrazovka **zpráva-10**
  * Úvodní obrazovka pro CICS
  * Obrazovka "Clear" nebo **prázdná** obrazovka

<a name="create-plans"></a>

### <a name="create-plans"></a>Vytvoření plánů

1. Na panelu nástrojů nástroje pro návrh 3270 vyberte možnost **Navigace** , takže zadáte navigační režim.

1. Chcete-li zahájit plán, v **navigačním** podokně vyberte možnost **Nový plán**.

1. V části **zvolit název nového plánu**zadejte název vašeho plánu. V seznamu **typ** vyberte typ plánu:

   | Typ plánu | Popis |
   |-----------|-------------|
   | **Přihlášení** | Pro samostatné nebo kombinované plány |
   | **Připojení** | Pro plány připojení |
   | **Dobu** | Pro plány odpojení |
   |||

1. Z podokna **hostitelské obrazovky** přetáhněte zachycené miniatury na plochu navigačního plánu v **navigačním** podokně.

   Pro reprezentaci prázdné obrazovky, kde zadáváte název transakce, použijte obrazovku "prázdné".

1. Uspořádejte obrazovky v pořadí, které popisuje úlohu, kterou definujete.

1. Chcete-li definovat cestu toku mezi obrazovkami, včetně rozvětvení a spojení, na panelu nástrojů pro návrh vyberte možnost **tok**.

1. Vyberte první obrazovku v toku. Přetáhněte připojení k další obrazovce v toku a nakreslete ho.

1. Pro každou obrazovku zadejte hodnoty vlastnosti **klíče podpory** (identifikátor upozornění) a vlastnost **fixed text** , která posune tok na další obrazovku.

   Můžete mít jenom klávesu s podporou, nebo i klávesu s podporou a pevný text.

Po dokončení plánu navigace můžete [metody definovat v dalším režimu](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Příklad

V tomto příkladu Předpokládejme, že spustíte transakci CICS s názvem "WBGB", která má tyto kroky: 

* Na první obrazovce zadejte jméno a účet.
* Na druhé obrazovce získáte zůstatek účtu.
* Skončíte na obrazovku "prázdná".
* Odhlásíte se z CICS na obrazovku "MSG-10".

Předpokládejme také, že tento postup zopakujete, ale zadáte nesprávná data, abyste mohli zachytit obrazovku, která zobrazuje chybu. Tady jsou obrazovky, které zachytíte:

* MSG-10
* CICS Welcome
* Prázdné
* WBGB_1 (vstup)
* WBGB_2 (chyba)
* Empty_1
* MSG-10_1

I když mnoho obrazovek získá jedinečné názvy, některé obrazovky jsou stejné obrazovky, například "MSG-10" a "Empty". Pro opakovanou obrazovku použijte pro tuto obrazovku v plánu jenom jednu instanci. Tady jsou příklady, které ukazují, jak může vypadat samostatný plán, plán připojení, plán odpojení a kombinovaný plán:

* Samostatný plán

  ![Samostatný navigační plán](./media/connectors-create-api-3270/standalone-plan.png)

* Připojit plán

  ![Připojit plán](./media/connectors-create-api-3270/connect-plan.png)

* Plán odpojení

  ![Plán odpojení](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinovaný plán

  ![Kombinovaný plán](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Příklad: Identifikovat opakované obrazovky

Aby konektor mohl navigovat a odlišit obrazovky, obvykle se na obrazovce nachází jedinečný text, který můžete použít jako identifikátor napříč zachycenými obrazovkami. Pro opakované obrazovky možná budete potřebovat více metod identifikace. Vzorový plán obsahuje rozvětvení, kde můžete získat obrazovky, které vypadají podobně. Jedna obrazovka vrátí zůstatek účtu a druhá obrazovka vrátí chybovou zprávu.

Nástroj pro návrh umožňuje přidat atributy rozpoznávání, například název obrazovky s názvem "získat zůstatek účtu" pomocí editoru pro rozpoznávání obrazovky. V případě podobných obrazovek budete potřebovat další atributy. V době běhu konektor používá tyto atributy k určení větve a rozvětvení.

* Ve větvi, která vrací platný vstup, což je obrazovka se zůstatkem účtu, můžete přidat pole, které má podmínku "neprázdné".

* Ve větvi, která se vrátí k chybě, můžete přidat pole, které má podmínku "Empty".

<a name="define-method"></a>

## <a name="define-methods"></a>Definovat metody

V tomto režimu definujete metodu, která je přidružena k vašemu navigačnímu plánu. Pro každý parametr metody zadáte datový typ, například řetězec, celé číslo, datum nebo čas a tak dále. Až budete hotovi, můžete otestovat svou metodu na hostiteli v reálném čase a potvrdit, že metoda funguje podle očekávání. Pak vygenerujete soubor metadat nebo soubor XML pro návrháře Integration Designer (HIDX), který nyní obsahuje definice metod, které se mají použít pro vytvoření a spuštění akce pro konektor IBM 3270.

1. Na panelu nástrojů nástroje pro návrh 3270 vyberte **metody** , takže zadáte režim metody. 

1. V **navigačním** podokně vyberte obrazovku, která má vstupní pole, která chcete.

1. Chcete-li přidat první vstupní parametr pro metodu, postupujte podle těchto kroků:

   1. V podokně **zachycení** na obrazovce emulátoru 3270 vyberte celé pole, ne pouze text v poli, které chcete jako první vstup.

      > [!TIP]
      > Chcete-li zobrazit všechna pole a ujistit se, že jste vybrali pole Dokončeno, v nabídce **zobrazení** vyberte možnost **všechna pole**.

   1. Na panelu nástrojů návrhového nástroje vyberte **vstupní pole**. 

   Chcete-li přidat více vstupních parametrů, opakujte předchozí kroky pro každý parametr.

1. Chcete-li přidat první výstupní parametr pro metodu, postupujte podle těchto kroků:

   1. V podokně **zachycení** na obrazovce emulátoru 3270 vyberte celé pole, ne pouze text v poli, které chcete jako první výstup.

      > [!TIP]
      > Chcete-li zobrazit všechna pole a ujistit se, že jste vybrali pole Dokončeno, v nabídce **zobrazení** vyberte možnost **všechna pole**.

   1. Na panelu nástrojů nástroje pro návrh vyberte **výstupní pole**.

   Chcete-li přidat více výstupních parametrů, opakujte předchozí kroky pro každý parametr.

1. Po přidání všech parametrů metody definujte tyto vlastnosti pro každý parametr:

   | Název vlastnosti | Možné hodnoty | 
   |---------------|-----------------|
   | **Datový typ** | Byte, datum a čas, desetinné číslo, int, Long, Short, řetězec |
   | **Technika vyplňování polí** | Parametry podporují tyto typy výplně a v případě potřeby plní prázdné hodnoty: <p><p>- **Zadejte**: Zadejte do pole postupně znaky. <p>- **Vyplnit**: V případě potřeby nahraďte obsah pole znaky, a to tak, že vyplníte prázdné znaky. <p>- **EraseEofType**: Vymažte pole a potom do pole zadejte postupně znaky. |
   | **Řetězec formátu** | Některé typy dat parametrů používají formátovací řetězec, který informuje konektor 3270, jak převést text z obrazovky na datový typ .NET: <p><p>- **Datum a čas**: Řetězec formátu data a času následuje za [řetězci vlastního formátu data a času rozhraní .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Datum `06/30/2019` například používá řetězec `MM/dd/yyyy`formátu. <p>- **Desetinné číslo**: Řetězec formátu Decimal používá [klauzuli obrázku COBOL](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Číslo `100.35` například používá řetězec `999V99`formátu. |
   |||

## <a name="save-and-view-metadata"></a>Uložení a zobrazení metadat

Po definování metody, ale před otestováním metody, uložte všechny informace, které jste dosud definovali, do souboru RAP (. rap).
Do tohoto souboru RAP se můžete kdykoli uložit v jakémkoli režimu. Nástroj pro návrh zahrnuje také ukázkový soubor RAP, který můžete otevřít a zkontrolovat tak, že v tomto umístění přejdete do instalační složky nástroje pro návrh a otevřete soubor WoodgroveBank. rap:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Pokud se ale pokusíte uložit změny do ukázkového souboru RAP nebo vygenerovat soubor HIDX ze vzorového souboru RAP, zatímco soubor zůstane v instalační složce Nástroje pro návrh, může se zobrazit chyba "přístup byl odepřen". Ve výchozím nastavení se nástroj pro návrh nainstaluje do složky Program Files bez zvýšených oprávnění. Pokud se zobrazí chyba, zkuste jedno z následujících řešení:

* Zkopírujte ukázkový soubor do jiného umístění.
* Spusťte nástroj pro návrh jako správce.
* Nastavte vlastníka složky sady SDK sami.

## <a name="test-your-method"></a>Otestování metody

1. Chcete-li spustit metodu proti aktivnímu hostiteli, a to i v režimu metod, stiskněte klávesu F5 nebo z panelu nástrojů pro návrh vyberte možnost **Spustit**.

   > [!TIP]
   > Režimy můžete kdykoli změnit. V nabídce **soubor** vyberte možnost **režim**a potom vyberte požadovaný režim.

1. Zadejte hodnoty Parameters a klikněte na **tlačítko OK**.

1. Chcete-li pokračovat na další obrazovku, klikněte na tlačítko **Další**.

1. Až budete hotovi, vyberte **Hotovo**, které zobrazuje vaše výstupní hodnoty parametrů.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generování a nahrání souboru HIDX

Až budete připraveni, vygenerujte soubor HIDX, abyste ho mohli nahrát na účet pro integraci. Nástroj pro návrh 3270 vytvoří soubor HIDX v nové podsložce, kam jste uložili soubor RAP.

1. Na panelu nástrojů nástroje pro návrh 3270 vyberte možnost **generovat kód**.

1. Přejít do složky, která obsahuje soubor RAP, a otevřete podsložku, kterou nástroj vytvořil po vygenerování souboru HIDX. Potvrďte, že nástroj vytvořil soubor HIDX.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a Najděte účet pro integraci.

1. Přidejte soubor HIDX jako mapu k účtu pro integraci, a to pomocí [těchto podobných kroků pro přidání map](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ale když vyberete typ mapy, vyberte **HIDX**.

Později v tomto tématu když do aplikace logiky přidáte akci IBM 3270 poprvé, budete vyzváni k vytvoření připojení mezi aplikací logiky a hostitelským serverem poskytnutím informací o připojení, jako jsou názvy pro účet pro integraci a hostitelský server. . Po vytvoření připojení můžete vybrat dříve přidaný soubor HIDX, metodu, kterou chcete spustit, a parametry, které chcete použít.

Po dokončení všech těchto kroků můžete použít akci, kterou vytvoříte v aplikaci logiky pro připojení k vašemu sálovému počítači IBM, diskovou obrazovku pro vaši aplikaci, zadat data, vrátit výsledky atd. Můžete také pokračovat v přidávání dalších akcí do aplikace logiky pro integraci s jinými aplikacemi, službami a systémy.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Spuštění akcí IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**a vyberte **přidat akci**. 

1. V poli Hledat vyberte možnost **Enterprise**. Do vyhledávacího pole zadejte jako filtr "3270". V seznamu akce vyberte tuto akci: **Spustí sálový program přes připojení TN3270.**

   ![Vybrat akci 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Pokud žádné připojení ještě neexistuje, zadejte pro připojení potřebné informace a klikněte na **vytvořit**.

   | Vlastnost | Požadováno | Value | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | <*název připojení*> | Název připojení |
   | **ID účtu pro integraci** | Ano | <*integration-account-name*> | Název vašeho účtu pro integraci |
   | **Adresa URL SAS účtu pro integraci** | Ano | <*integration-account-SAS-URL*> | Adresa URL sdíleného přístupového podpisu (SAS) vašeho účtu pro integraci, kterou můžete vygenerovat z nastavení účtu integrace v Azure Portal. <p>1. V nabídce účtu pro integraci vyberte v části **Nastavení**možnost **Adresa URL zpětného volání**. <br>2. V pravém podokně zkopírujte vygenerovanou hodnotu **URL zpětného volání** . |
   | **Server** | Ano | <*TN3270-server-name*> | Název serveru pro vaši službu TN3270 |
   | **Port** | Ne | <*TN3270-server-port*> | Port používaný serverem TN3270 Pokud necháte pole prázdné, použije `23` konektor jako výchozí hodnotu. |
   | **Typ zařízení** | Ne | <*IBM-Terminal-model*> | Název nebo číslo modelu terminálu IBM pro emulaci. Pokud necháte pole prázdné, použije konektor výchozí hodnoty. |
   | **Znaková stránka** | Ne | <*číslo stránky kódu*> | Číslo kódové stránky pro hostitele Pokud necháte pole prázdné, použije `37` konektor jako výchozí hodnotu. |
   | **Název logické jednotky** | Ne | <*logická jednotka – název*> | Název konkrétní logické jednotky, která se má požadovat od hostitele |
   | **Povolit SSL?** | Ne | Zapnuto nebo vypnuto | Zapněte nebo vypněte šifrování SSL. |
   | **Ověřit certifikát SSL hostitele?** | Ne | Zapnuto nebo vypnuto | Zapněte nebo vypněte ověřování pro certifikát serveru. |
   ||||

   Příklad:

   ![Vlastnosti připojení](./media/connectors-create-api-3270/connection-properties.png)

1. Zadejte potřebné informace pro tuto akci:

   | Vlastnost | Požadováno | Value | Popis |
   |----------|----------|-------|-------------|
   | **Název HIDX** | Ano | <*HIDX-název souboru*> | Vyberte soubor HIDX 3270, který chcete použít. |
   | **Název metody** | Ano | <*název metody*> | Vyberte metodu v souboru HIDX, který chcete použít. Po výběru metody se zobrazí seznam **Přidat nový parametr** , takže můžete vybrat parametry pro použití s touto metodou. |
   ||||

   Příklad:

   **Vybrat soubor HIDX**

   ![Vybrat soubor HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Vyberte metodu**

   ![Vybrat metodu](./media/connectors-create-api-3270/select-method.png)

   **Vyberte parametry**

   ![Vybrat parametry](./media/connectors-create-api-3270/add-parameters.png)

1. Až skončíte, uložte a spusťte aplikaci logiky.

   Po dokončení běhu aplikace logiky se zobrazí kroky z tohoto spuštění. 
   Úspěšné kroky zobrazují značky zaškrtnutí, zatímco neúspěšné kroky ukazují písmeno "X".

1. Chcete-li zkontrolovat vstupy a výstupy pro jednotlivé kroky, rozbalte tento krok.

1. Chcete-li zkontrolovat výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/si3270/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
