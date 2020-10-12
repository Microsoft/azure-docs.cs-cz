---
title: Posílání a přijímání zpráv X12 pro B2B
description: Zprávy Exchange X12 pro scénáře integrace B2B Enterprise pomocí Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 61f3f2af61bc24f76d061de672a3eaacd54f7f0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87066119"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy Exchange X12 pro integraci B2B Enterprise v Azure Logic Apps s využitím Enterprise Integration Pack

Pokud chcete pracovat se X12 zprávami v Azure Logic Apps, můžete použít konektor X12, který poskytuje triggery a akce pro správu komunikace X12. Informace o EDIFACT zprávách najdete v tématu [zprávy Exchange EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze které chcete použít konektor X12, a Trigger, který spouští pracovní postup vaší aplikace logiky. Konektor X12 poskytuje pouze akce, nikoli triggery. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a propojený s aplikací logiky, ve které plánujete použít konektor X12. Vaše aplikace logiky i účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

* Aspoň dva [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jste už definovali v účtu pro integraci pomocí kvalifikátoru identity X12.

* [Schémata](../logic-apps/logic-apps-enterprise-integration-schemas.md) , která se mají použít pro ověření XML, které jste už přidali do účtu pro integraci. Pokud pracujete se schématy přenositelnosti zdravotního pojištění a jednáním Act (HIPAA), přečtěte si téma [schémata HIPAA](#hipaa-schemas).

* Než budete moct použít konektor X12, musíte vytvořit X12 [smlouvu](../logic-apps/logic-apps-enterprise-integration-agreements.md) mezi vašimi obchodními partnery a uložit tuto smlouvu na účet pro integraci. Pokud pracujete se schématy přenositelnosti zdravotního pojištění a HIPAA (dodržování zodpovědnosti), musíte `schemaReferences` do smlouvy přidat oddíl. Další informace najdete v tématu [schémata HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Nastavení příjmu

Po nastavení vlastností smlouvy můžete nakonfigurovat způsob, jakým tato smlouva identifikuje a zpracovává příchozí zprávy, které od svého partnera obdržíte prostřednictvím této smlouvy.

1. V části **Přidat**vyberte **Nastavení příjmu**.

1. Tyto vlastnosti můžete nakonfigurovat na základě vaší smlouvy s partnerem, který s vámi vyměňuje zprávy. **Nastavení příjmu** jsou uspořádána do těchto částí:

   * [Identifikátory](#inbound-identifiers)
   * [Potvrzení](#inbound-acknowledgement)
   * [Schémata](#inbound-schemas)
   * [Obálek](#inbound-envelopes)
   * [Řídicí čísla](#inbound-control-numbers)
   * [Ověřování](#inbound-validations)
   * [Interní nastavení](#inbound-internal-settings)

   Popis vlastností najdete v tabulkách v této části.

1. Až to budete mít, nezapomeňte nastavení uložit tak, že vyberete **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Nastavení příjmu – identifikátory

![Vlastnosti identifikátoru pro příchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Vlastnost | Popis |
|----------|-------------|
| **ISA1 (kvalifikátor autorizace)** | Hodnota kvalifikátoru autorizace, kterou chcete použít. Výchozí hodnota je **00-nejsou k dispozici žádné informace o autorizaci**. <p>**Poznámka**: Pokud vyberete jiné hodnoty, zadejte hodnotu pro vlastnost **ISA2** . |
| **ISA2** | Hodnota informací o autorizaci, která se má použít, pokud vlastnost **ISA1** není **00 – nejsou k dispozici žádné informace o autorizaci**. Hodnota této vlastnosti musí mít minimálně jeden alfanumerický znak a maximálně 10. |
| **ISA3 (kvalifikátor zabezpečení)** | Hodnota kvalifikátoru zabezpečení, kterou chcete použít. Výchozí hodnota je **00 – nejsou k dispozici žádné informace o zabezpečení**. <p>**Poznámka**: Pokud vyberete jiné hodnoty, zadejte hodnotu pro vlastnost **ISA4** . |
| **ISA4** | Hodnota informací o zabezpečení, která se má použít, pokud vlastnost **ISA3** není **00 – nejsou k dispozici žádné informace o zabezpečení**. Hodnota této vlastnosti musí mít minimálně jeden alfanumerický znak a maximálně 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Nastavení příjmu – potvrzení

![Potvrzení příchozích zpráv](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Vlastnost | Popis |
|----------|-------------|
| **Očekáván TA1** | Vrátí technické potvrzení (TA1) odesílateli výměny. |
| **Očekává se FA.** | Vrátí funkční potvrzení (IM) odesílateli výměny. <p>Pro vlastnost **verze im** na základě verze schématu vyberte potvrzení 997 nebo 999. <p>Pokud chcete povolit generování smyček AK2 ve funkčních potvrzování pro přijaté sady transakcí, vyberte **Zahrnout AK2/IK2 Loop**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Nastavení příjmu – schémata

![Schémata pro příchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Pro tuto část vyberte [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) z [účtu integrace](./logic-apps-enterprise-integration-create-integration-account.md) pro každý typ transakce (ST01) a aplikace odesílatele (GS02). Kanál pro příjem EDI provede zpětný překlad příchozí zprávy porovnáním hodnot a schématu, které jste nastavili v této části, s hodnotami pro ST01 a GS02 v příchozí zprávě a se schématem příchozí zprávy. Po dokončení každého řádku se automaticky zobrazí nový prázdný řádek.

| Vlastnost | Popis |
|----------|-------------|
| **Verze** | Verze X12 schématu |
| **Typ transakce (ST01)** | Typ transakce |
| **Aplikace odesílatele (GS02)** | Aplikace odesílatel |
| **Schéma** | Soubor schématu, který chcete použít |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Nastavení příjmu – obálky

![Oddělovače, které se použijí v sadách transakcí pro příchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Vlastnost | Popis |
|----------|-------------|
| **Využití ISA11** | Oddělovač, který se má použít v sadě transakcí: <p>- **Standardní identifikátor**: pro Desítkový zápis použijte tečku (.) a místo desítkového zápisu příchozího dokumentu v kanálu pro příjem EDI. <p>- **Oddělovač opakování**: Určete oddělovač pro opakované výskyty jednoduchého datového elementu nebo opakované datové struktury. Například obvykle se jako oddělovač opakování používá kosočtverce (^). Pro schémata HIPAA můžete použít jenom kosočtverce. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Nastavení příjmu – kontrolní čísla

![Zpracování duplicitních kontrolních čísel pro příchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Vlastnost | Popis |
|----------|-------------|
| **Zakázat duplicity kontrolního čísla výměny** | Zablokuje duplicitní změny. U přijatého kontrolního čísla výměny ověřte řídící číslo výměny (ISA13). Pokud je nalezena shoda, kanál příjmu EDI nezpracovává výměnu. <p><p>Chcete-li zadat počet dní, po které má být provedena tato kontroly, zadejte hodnotu pro vlastnost **Check for duplicitní ISA13 každých (days)** . |
| **Zakázat duplicity kontrolních čísel skupin** | Zablokuje změny, které mají duplicitní čísla řízení skupiny. |
| **Zakázat duplicity kontrolního čísla sady transakcí** | Zablokuje změny, které mají duplicitní čísla řízení sady transakcí. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Nastavení příjmu – ověření

![Ověření pro příchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**Výchozí** řádek zobrazuje pravidla ověřování, která se používají pro typ zprávy EDI. Chcete-li definovat jiná pravidla, vyberte jednotlivá pole, kde má být pravidlo nastaveno na **hodnotu true**. Po dokončení každého řádku se automaticky zobrazí nový prázdný řádek.

| Vlastnost | Popis |
|----------|-------------|
| **Typ zprávy** | Typ zprávy EDI |
| **Ověřování EDI** | Proveďte ověření EDI u datových typů, které jsou definovány vlastnostmi EDI schématu, omezením délky, prázdnými datovými prvky a koncovými oddělovači. |
| **Rozšířené ověřování** | Pokud datový typ není EDI, ověřování je na základě požadavku datového prvku a povoleného opakování, výčtů a ověřování délky datových prvků (min nebo max). |
| **Povolení počátečních a koncových nul** | Ponechejte jakékoli další úvodní nebo koncové znaky nula a mezer. Tyto znaky neodstraňujte. |
| **Oříznout úvodní a koncové nuly** | Odebere všechny úvodní nebo koncové znaky nula a mezer. |
| **Zásady koncového oddělovače** | Vygeneruje oddělovače na konci. <p>- **Nepovoleno**: zakáže koncové oddělovače a oddělovače v příchozím přenosu. V případě, že výměna obsahuje koncové oddělovače a oddělovače, je výměna deklarována jako neplatná. <p>- **Volitelné**: přijmout zaměnitelné s nebo bez ukončovacích oddělovačů a oddělovačů. <p>- **Povinné**: Výměna příchozích dat musí mít koncové oddělovače a oddělovače. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Nastavení příjmu – interní nastavení

![Interní nastavení příchozích zpráv](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Vlastnost | Popis |
|----------|-------------|
| **Převede implicitní desítkový formát NN na základní 10 numerickou hodnotu.** | Převeďte číslo EDI zadané ve formátu "NN" na číselnou hodnotu o základu 10. |
| **Pokud jsou povolené koncové oddělovače, vytvoří se prázdné značky XML.** | Má odesílatel výměny do koncových oddělovačů zahrnovat prázdné značky XML. |
| **Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě** | Analyzujte každou sadu transakcí, která je ve výměně, do samostatného dokumentu XML použitím příslušné obálky pro sadu transakcí. Pozastaví pouze transakce, kde se ověření nepovede. |
| **Rozdělit výměnu jako sady transakcí – při chybě pozastavit výměnu** | Analyzujte každou sadu transakcí, která je ve výměně, do samostatného dokumentu XML použitím příslušné obálky. Pokud jedna nebo více transakcí v rámci výměny selže, pozastavíte celý výměnu. |
| **Zachovat výměnu – pozastavit sady transakcí při chybě** | Ponechte si výměnu beze změn a vytvořte dokument XML pro celý vydaný hromadnou výměnu. Pozastaví pouze sady transakcí, jejichž ověření se nezdaří, ale pokračuje ve zpracování všech ostatních sad transakcí. |
| **Zachovat výměnu – pozastavit výměnu při chybě** |Ponechá výměnu beze změn, vytvoří dokument XML pro celý vydaný hromadnou výměnu. Pozastaví celý výměnu, pokud jedna nebo více transakcí v rámci výměny selže. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Nastavení odesílání

Po nastavení vlastností smlouvy můžete nakonfigurovat způsob, jakým tato smlouva identifikuje a zpracovává odchozí zprávy odeslané na svého partnera prostřednictvím této smlouvy.

1. V části **Přidat**vyberte **Odeslat nastavení**.

1. Tyto vlastnosti můžete nakonfigurovat na základě vaší smlouvy s partnerem, který s vámi vyměňuje zprávy. Popis vlastností najdete v tabulkách v této části.

   **Nastavení odeslání** jsou uspořádána do těchto částí:

   * [Identifikátory](#outbound-identifiers)
   * [Potvrzení](#outbound-acknowledgement)
   * [Schémata](#outbound-schemas)
   * [Obálek](#outbound-envelopes)
   * [Číslo verze ovládacího prvku](#outbound-control-version-number)
   * [Řídicí čísla](#outbound-control-numbers)
   * [Znakové sady a oddělovače](#outbound-character-sets-separators)
   * [Ověřování](#outbound-validation)

1. Až to budete mít, nezapomeňte nastavení uložit tak, že vyberete **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Nastavení odesílání – identifikátory

![Vlastnosti identifikátoru pro odchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Vlastnost | Popis |
|----------|-------------|
| **ISA1 (kvalifikátor autorizace)** | Hodnota kvalifikátoru autorizace, kterou chcete použít. Výchozí hodnota je **00-nejsou k dispozici žádné informace o autorizaci**. <p>**Poznámka**: Pokud vyberete jiné hodnoty, zadejte hodnotu pro vlastnost **ISA2** . |
| **ISA2** | Hodnota informací o autorizaci, která se má použít, pokud vlastnost **ISA1** není **00 – nejsou k dispozici žádné informace o autorizaci**. Hodnota této vlastnosti musí mít minimálně jeden alfanumerický znak a maximálně 10. |
| **ISA3 (kvalifikátor zabezpečení)** | Hodnota kvalifikátoru zabezpečení, kterou chcete použít. Výchozí hodnota je **00 – nejsou k dispozici žádné informace o zabezpečení**. <p>**Poznámka**: Pokud vyberete jiné hodnoty, zadejte hodnotu pro vlastnost **ISA4** . |
| **ISA4** | Hodnota informací o zabezpečení, která se má použít, pokud vlastnost **ISA3** není **00 – nejsou k dispozici žádné informace o zabezpečení**. Hodnota této vlastnosti musí mít minimálně jeden alfanumerický znak a maximálně 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Nastavení odesílání – potvrzení

![Vlastnosti potvrzení odchozích zpráv](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Vlastnost | Popis |
|----------|-------------|
| **Očekáván TA1** | Vrátí technické potvrzení (TA1) odesílateli výměny. <p>Toto nastavení určuje, že hostitel partner, který posílá zprávu, požádá o potvrzení od hostovaného partnera v rámci smlouvy. Tato potvrzení jsou očekávána hostitelským partnerem na základě nastavení přijetí smlouvy. |
| **Očekává se FA.** | Vrátí funkční potvrzení (IM) odesílateli výměny. Pro vlastnost **verze im** na základě verze schématu vyberte potvrzení 997 nebo 999. <p>Toto nastavení určuje, že hostitelský partner, který posílá zprávu, požádá o potvrzení od hostovaného partnera v rámci smlouvy. Tato potvrzení jsou očekávána hostitelským partnerem na základě nastavení přijetí smlouvy. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Nastavení odesílání – schémata

![Schémata pro odchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Pro tuto část vyberte [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) z [účtu integrace](./logic-apps-enterprise-integration-create-integration-account.md) pro každý typ transakce (ST01). Po dokončení každého řádku se automaticky zobrazí nový prázdný řádek.

| Vlastnost | Popis |
|----------|-------------|
| **Verze** | Verze X12 schématu |
| **Typ transakce (ST01)** | Typ transakce pro schéma |
| **Schéma** | Soubor schématu, který chcete použít. Pokud nejprve vyberete schéma, je automaticky nastavena verze a typ transakce. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Nastavení odesílání – obálky

![Oddělovače v transakci nastavené pro použití pro odchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Vlastnost | Popis |
|----------|-------------|
| **Využití ISA11** | Oddělovač, který se má použít v sadě transakcí: <p>- **Standardní identifikátor**: pro Desítkový zápis použijte tečku (.) a místo desítkového zápisu odchozího dokumentu v kanálu pro odesílání EDI. <p>- **Oddělovač opakování**: Určete oddělovač pro opakované výskyty jednoduchého datového elementu nebo opakované datové struktury. Například obvykle se jako oddělovač opakování používá kosočtverce (^). Pro schémata HIPAA můžete použít jenom kosočtverce. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Odeslat nastavení – číslo verze ovládacího prvku

![Řídicí číslo verze pro odchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Pro tuto část vyberte [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) z [účtu integrace](./logic-apps-enterprise-integration-create-integration-account.md) pro každý výměnný účet. Po dokončení každého řádku se automaticky zobrazí nový prázdný řádek.

| Vlastnost | Popis |
|----------|-------------|
| **Číslo verze ovládacího prvku (ISA12)** | Verze standardu X12 |
| **Indikátor využití (ISA15)** | Kontext výměny, což je buď **testovací** data, **informační** data nebo **produkční** data |
| **Schéma** | Schéma, které se má použít k vytváření segmentů GS a ST pro X12 kódovaný pro výměnu, který se odesílá do kanálu pro odesílání EDI. |
| **GS1** | Volitelné, vyberte funkční kód. |
| **GS2 úrovně** | Volitelně zadejte odesílatele aplikace. |
| **GS3 úrovně** | Volitelné, určete příjemce aplikace. |
| **GS4 úrovně** | Volitelné, vyberte **CCYYMMDD** nebo **rrmmdd**. |
| **GS5** | Volitelné, vyberte **hhmm**, **HHMMSS**nebo **HHMMSSdd**. |
| **GS7** | Volitelné, vyberte hodnotu odpovědného úřadu. |
| **GS8** | Volitelné, zadejte verzi dokumentu schématu. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Odeslat nastavení – kontrolní čísla

![Řídicí čísla pro odchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Vlastnost | Popis |
|----------|-------------|
| **Kontrolní číslo výměny (ISA13)** | Rozsah hodnot pro kontrolní číslo výměny, který může mít minimální hodnotu 1 a maximální hodnotu 999999999 |
| **Řídicí číslo skupiny (GS06)** | Rozsah hodnot pro kontrolní číslo skupiny, který může mít minimální hodnotu 1 a maximální hodnotu 999999999 |
| **Kontrolní číslo sady transakcí (ST02)** | Rozsah hodnot pro kontrolní číslo sady transakcí, který může mít minimální hodnotu 1 a maximální hodnotu 999999999 <p>- **Prefix**: volitelné, alfanumerické hodnoty <br>- **Přípona**: volitelné, alfanumerické hodnoty |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Odeslat nastavení – znakové sady a oddělovače

![Oddělovače pro typy zpráv v odchozích zprávách](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**Výchozí** řádek zobrazuje znakovou sadu, která se používá jako oddělovače pro schéma zprávy. Pokud nechcete používat **výchozí** znakovou sadu, můžete pro každý typ zprávy zadat jinou sadu oddělovačů. Po dokončení každého řádku se automaticky zobrazí nový prázdný řádek.

> [!TIP]
> Chcete-li zadat speciální hodnoty znaků, upravte smlouvu jako JSON a zadejte hodnotu ASCII pro speciální znak.

| Vlastnost | Popis |
|----------|-------------|
| **Znaková sada, která se má použít** | X12 znaková sada, která je buď **Basic**, **Extended**nebo **UTF8**. |
| **Schéma** | Schéma, které chcete použít. Po výběru schématu vyberte znakovou sadu, kterou chcete použít, na základě níže uvedených popisů oddělovače. |
| **Typ vstupu** | Vstupní typ znakové sady |
| **Oddělovač komponent** | Jeden znak, který odděluje složené datové prvky |
| **Oddělovač datových prvků** | Jeden znak, který odděluje jednoduché datové prvky v rámci složených dat |
| **Oddělovač znaků pro nahrazení** | Náhradní znak, který nahradí všechny znaky oddělovače v datech datové části při generování odchozí zprávy X12 |
| **Ukončovací znak segmentu** | Jeden znak, který označuje konec segmentu EDI |
| **Auditování** | Znak, který má být použit s identifikátorem segmentu. Pokud zadáte příponu, může být datový prvek koncového znaku segmentu prázdný. Pokud je ukončovací znak segmentu prázdný, je nutné určit příponu. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Nastavení odeslání – ověření

![Vlastnosti ověření pro odchozí zprávy](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**Výchozí** řádek zobrazuje pravidla ověřování, která se používají pro typ zprávy EDI. Chcete-li definovat jiná pravidla, vyberte jednotlivá pole, kde má být pravidlo nastaveno na **hodnotu true**. Po dokončení každého řádku se automaticky zobrazí nový prázdný řádek.

| Vlastnost | Popis |
|----------|-------------|
| **Typ zprávy** | Typ zprávy EDI |
| **Ověřování EDI** | Proveďte ověření EDI u datových typů, které jsou definovány vlastnostmi EDI schématu, omezením délky, prázdnými datovými prvky a koncovými oddělovači. |
| **Rozšířené ověřování** | Pokud datový typ není EDI, ověřování je na základě požadavku datového prvku a povoleného opakování, výčtů a ověřování délky datových prvků (min nebo max). |
| **Povolení počátečních a koncových nul** | Ponechejte jakékoli další úvodní nebo koncové znaky nula a mezer. Tyto znaky neodstraňujte. |
| **Oříznout úvodní a koncové nuly** | Odebere všechny úvodní nebo koncové znaky nula a mezer. |
| **Zásady koncového oddělovače** | Vygeneruje oddělovače na konci. <p>- **Nepovoleno**: zakáže koncové oddělovače a oddělovače v odchozím přenosu. V případě, že výměna obsahuje koncové oddělovače a oddělovače, je výměna deklarována jako neplatná. <p>- **Volitelné**: odešlete změny s koncovými oddělovači a oddělovači nebo bez nich. <p>- **Povinné**: odchozí výměna musí mít koncové oddělovače a oddělovače. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Schémata HIPAA a typy zpráv

Když pracujete se schématy HIPAA a typy zpráv 277 nebo 837, je nutné provést několik dalších kroků. [Čísla verzí dokumentů (GS8)](#outbound-control-version-number) pro tyto typy zpráv mají více než 9 znaků, například "005010X222A1". Kromě toho jsou některá čísla verzí dokumentu mapována na typy Variant typu variant. Pokud neodkazujte na správný typ zprávy ve schématu a ve vaší smlouvě, zobrazí se tato chybová zpráva:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Tato tabulka uvádí seznam ovlivněných zpráv, všechny varianty a čísla verzí dokumentů, která jsou namapována na tyto typy zpráv:

| Typ nebo varianta zprávy |  Description | Číslo verze dokumentu (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Oznámení o stavu informací o zdravotní péči | 005010X212 |
| 837_I | Institucionální deklarace identity zdravotnictví | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Deklarace identity zdravotnictví zubního lékaře | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Specialista na nárok na zdravotní péči | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Také je nutné zakázat ověřování EDI při použití těchto čísel verzí dokumentu, protože výsledkem je chyba, že délka znaku není platná.

Chcete-li zadat tato čísla verzí dokumentů a typy zpráv, postupujte podle následujících kroků:

1. V HIPAA schématu nahraďte typ aktuální zprávy typem zprávy variant pro číslo verze dokumentu, který chcete použít.

   Předpokládejme například, že chcete použít číslo verze dokumentu `005010X222A1` s `837` typem zprávy. Ve schématu nahraďte všechny `"X12_00501_837"` hodnoty `"X12_00501_837_P"` hodnotou místo toho.

   K aktualizaci schématu použijte následující postup:

   1. V Azure Portal přejít na účet pro integraci. Najděte své schéma a Stáhněte si ho. Nahraďte typ zprávy a přejmenujte soubor schématu a nahrajte změněné schéma na účet pro integraci. Další informace najdete v tématu [Úprava schémat](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. V části nastavení zprávy vaší smlouvy vyberte revidované schéma.

1. Do objektu vaší smlouvy `schemaReferences` přidejte další položku, která určuje typ zprávy variant odpovídající vašemu číslu verze dokumentu.

   Předpokládejme například, že chcete použít číslo verze dokumentu `005010X222A1` pro `837` typ zprávy. Vaše smlouva obsahuje `schemaReferences` oddíl s těmito vlastnostmi a hodnotami:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   V této `schemaReferences` části přidejte další položku, která má tyto hodnoty:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Až budete hotovi, vaše `schemaReferences` část bude vypadat takto:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. V nastavení zpráv vaší smlouvy zakažte ověřování EDI zrušením zaškrtnutí políčka **ověření EDI** buď pro každý typ zprávy, nebo pro všechny typy zpráv, pokud používáte **výchozí** hodnoty.

   ![Zakázat ověřování pro všechny typy zpráv nebo každý typ zprávy](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/x12/).

> [!NOTE]
> Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá [omezení zpráv B2B pro ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech pro Logic Apps](../connectors/apis-list.md)
