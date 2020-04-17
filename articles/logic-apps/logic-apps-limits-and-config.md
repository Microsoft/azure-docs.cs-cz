---
title: Omezení a konfigurace
description: Omezení služeb, jako je doba trvání, propustnost a kapacita, plus hodnoty konfigurace, jako jsou IP adresy, které je třeba povolit, pro aplikace Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: 40950be2e5caeb17d20086720a7b65c15147c2f5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535107"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informace o omezeních a konfiguraci pro Azure Logic Apps

Tento článek popisuje omezení a podrobnosti konfigurace pro vytváření a spouštění automatizovaných pracovních postupů pomocí Azure Logic Apps. Informace o power automatu najdete [v tématu Limity a konfigurace v power automatu](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Omezení definic

Tady jsou limity pro definici jedné aplikace logiky:

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce na pracovní postup | 500 | Chcete-li toto omezení rozšířit, můžete podle potřeby přidat vnořené pracovní postupy. |
| Povolená hloubka vnoření pro akce | 8 | Chcete-li toto omezení rozšířit, můžete podle potřeby přidat vnořené pracovní postupy. |
| Pracovní postupy podle oblasti na předplatné | 1 000 | |
| Aktivační události podle pracovního postupu | 10 | Při práci v zobrazení kódu není návrhář |
| Omezení počtu případů přepnutí oboru | 25 | |
| Proměnné na pracovní postup | 250 | |
| Znaky na výraz | 8 192 | |
| Maximální velikost pro`trackedProperties` | 16 000 znaků |
| Název `action` pro nebo`trigger` | 80 znaků | |
| Délka`description` | 256 znaků | |
| Maximální`parameters` | 50 | |
| Maximální`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Doba trvání běhu a limity uchovávání informací

Tady jsou limity pro spuštění jedné aplikace logiky:

| Název | Limit pro více klientů | Limit prostředí integrační ch služeb | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Doba trvání spuštění | 90 dnů | 366 dní | Doba trvání spuštění se vypočítá pomocí počátečního času spuštění a limitu určeného *v době zahájení* nastavením pracovního [**postupu, spuštěním uchovávání historie ve dnech**](#change-duration). <p><p>Chcete-li změnit výchozí limit, který je 90 dní, [přečtěte si](#change-duration)informace o době trvání běhu změn . |
| Spuštění uchování v úložišti | 90 dnů | 366 dní | Uchovávání spuštění se vypočítá pomocí počátečního času spuštění a limitu, který je zadán *v aktuálním čase* nastavením pracovního [**postupu, spustit uchovávání historie ve dnech**](#change-retention). Bez ohledu na to, zda je spuštění dokončeno nebo časový rozsah, výpočet uchovávání informací vždy používá čas zahájení spuštění. Pokud doba trvání spuštění překročí *aktuální* limit uchovávání informací, je spuštění odebráno z historie spuštění. <p><p>Pokud toto nastavení změníte, aktuální limit se vždy použije pro výpočet uchovávání informací bez ohledu na předchozí limit. Pokud například snížíte limit uchovávání informací z 90 dnů na 30 dní, bude z historie spuštění odebráno spuštění, které je staré 60 dní. Pokud produmíte dobu uchovávání z 30 dnů na 60 dní, běh, který je 20 dní starý pobyty v historii spuštění po dobu dalších 40 dnů. <p><p>Pokud chcete změnit výchozí limit, který je 90 dní, [přečtěte si informace o změně uchovávání spuštění v úložišti](#change-retention). |
| Minimální interval opakování | 1 sekunda | 1 sekunda ||
| Maximální interval opakování | 500 dnů | 500 dnů ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>Změna doby běhu a spuštění uchovávání informací v úložišti

Chcete-li změnit výchozí limit pro dobu trvání spuštění a spustit uchovávání v úložišti, postupujte takto. Chcete-li zvýšit maximální limit, [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) o pomoc s vašimi požadavky.

> [!NOTE]
> Pro aplikace logiky ve víceklientské Azure 90 denní výchozí limit je stejný jako maximální limit. Tuto hodnotu lze pouze snížit.
> Pro aplikace logiky v prostředí služby integrace můžete snížit nebo zvýšit výchozí limit 90 dnů.

1. Přejděte na [portál Azure](https://portal.azure.com). Ve vyhledávacím poli portálu najděte a vyberte **aplikace Logika**.

1. Vyberte a otevřete aplikaci logiky v Návrháři aplikace logiky.

1. V nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

1. V části **Možnosti běhu**vyberte v seznamu **Spustit uchovávání historie ve dnech** možnost **Vlastní**.

1. Přetažením jezdce změňte požadovaný počet dní.

1. Až budete hotovi, na panelu nástrojů **Nastavení pracovního postupu** vyberte **Uložit**.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Souběžnost, opakování a debatní limity

Tady jsou limity pro spuštění jedné aplikace logiky:

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Souběžnost aktivační události | - Neomezené, když je vypnuto řízení souběžnosti <p><p>- 25 je výchozí limit při řízení souběžnosti je zapnuta, které nelze vrátit po povolení souběžnosti. Můžete změnit výchozí hodnotu mezi 1 a 50 včetně. | Toto omezení popisuje nejvyšší počet instancí aplikace logiky, které lze spustit současně nebo paralelně. <p><p>**Poznámka:** Je-li souběžnost zapnuta, limit SplitOn se sníží na 100 položek pro [debatní pole](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Pokud chcete změnit výchozí limit na hodnotu mezi 1 a 50 inkluzívně, přečtěte si následující informace [o omezení souběžnosti aktivační](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) události nebo instance aktivační [události](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximální počet čekání | - Bez souběžnosti je minimální počet čekajících běhů 1, zatímco maximální počet je 50. <p><p>- S souběžnosti, minimální počet čekajících spuštění je 10 plus počet souběžných spuštění (aktivační událost souběžnosti). Maximální počet můžete změnit na 100 včetně. | Toto omezení popisuje nejvyšší počet instancí aplikace logiky, které můžete čekat na spuštění, když vaše aplikace logiky je již spuštěna maximální souběžné instance. <p><p>Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Foreach pole položky | 100 000 | Toto omezení popisuje nejvyšší počet položek pole, které může zpracovat smyčka "pro každou". <p><p>Chcete-li filtrovat větší pole, můžete použít [akci dotazu](logic-apps-perform-data-operations.md#filter-array-action). |
| Souběžnost Foreach | 20 je výchozí limit při vypnutí ovládacího prvku souběžnosti. Můžete změnit výchozí hodnotu mezi 1 a 50 včetně. | Toto omezení je nejvyšší počet iterací smyčky "pro každou", která může být spuštěna současně nebo paralelně. <p><p>Chcete-li změnit výchozí limit na hodnotu mezi 1 a 50 včetně, naleznete [v tématu Změna "pro každý" limit souběžnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) nebo [Spustit "pro každou" smyčky postupně](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Položky SplitOn | - 100 000 bez spouštěcí souběžnosti <p><p>- 100 s spouštěcí souběžnou | Pro aktivační události, které vracejí pole, můžete zadat výraz, který používá vlastnost SplitOn, která [rozděluje nebo debatuje položky pole do více instancí pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) pro zpracování, spíše než použít smyčku "Foreach". Tento výraz odkazuje na pole, které má být používáno pro vytvoření a spuštění instance pracovního postupu pro každou položku pole. <p><p>**Poznámka:** Pokud je souběžnost zapnuta, limit SplitOn se sníží na 100 položek. |
| Do iterací | - Výchozí: 60 <p><p>- Maximálně: 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Omezení propustnosti

Tady jsou limity pro definici jedné aplikace logiky:

### <a name="multi-tenant-logic-apps-service"></a>Služba Logic Apps pro více klientů

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce: Popravy za 5 minut | Výchozí limit je 100 000, ale maximální limit je 300 000. | Pokud chcete změnit výchozí limit, [přečtěte si informace o spuštění aplikace logiky v režimu "vysoká propustnost"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), který je ve verzi Preview. Nebo můžete distribuovat úlohy napříč více než jednu aplikaci logiky podle potřeby. |
| Akce: Souběžná odchozí volání | ~2500 | Můžete snížit počet souběžných požadavků nebo podle potřeby zkrátit dobu trvání. |
| Koncový bod runtime: Souběžná příchozí volání | ~1 000 | Můžete snížit počet souběžných požadavků nebo podle potřeby zkrátit dobu trvání. |
| Koncový bod runtime: Čtení hovorů za 5 minut  | 60 000 | Podle potřeby můžete distribuovat pracovní vytížení mezi více než jednu aplikaci. |
| Koncový bod runtime: Vyvolání volání za 5 minut | 45 000 | Podle potřeby můžete distribuovat pracovní vytížení mezi více než jednu aplikaci. |
| Propustnost obsahu za 5 minut | 600 MB | Podle potřeby můžete distribuovat pracovní vytížení mezi více než jednu aplikaci. |
||||

### <a name="integration-service-environment-ise"></a>Prostředí integračních služeb (ISE)

Tady jsou limity propustnost pro [sku prémiové služby ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

| Název | Omezení | Poznámky |
|------|-------|-------|
| Limit spuštění základní jednotky | Když kapacita infrastruktury dosáhne 80 % | Poskytuje přibližně 4 000 spuštění akce za minutu, což je ~160 milionů spuštění akcí za měsíc. | |
| Limit spuštění jednotky škálování | Když kapacita infrastruktury dosáhne 80 % | Každá jednotka škálování může poskytovat přibližně 2 000 dalších spuštění akce za minutu, což je o cca 80 milionů více spuštění akce za měsíc. | |
| Maximální velikostní jednotky, které můžete přidat | 10 | |
||||

Chcete-li přejít nad tyto limity v normálním zpracování nebo spustit zátěžové testování, které by mohly jít nad tyto limity, [obraťte](mailto://logicappsemail@microsoft.com) se na tým Logic Apps o pomoc s vašimi požadavky.

> [!NOTE]
> [Skladová položka ISE vývojáře ise](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) nemá žádná publikovaná omezení, žádné možnosti pro škálování a žádná smlouva o úrovni služeb (SLA). Tuto skladovou položku použijte pouze pro experimentování, vývoj a testování, nikoli pro testování výroby nebo výkonu.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limity brány

Azure Logic Apps podporuje operace zápisu, včetně vložení a aktualizace, prostřednictvím brány. Tyto operace však mají [omezení velikosti datové části](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>Http limity

Zde jsou limity pro jedno odchozí nebo příchozí volání HTTP:

#### <a name="timeout"></a>Časový limit

Některé operace konektoru provádět asynchronní volání nebo naslouchat požadavky webhooku, takže časový limit pro tyto operace může být delší než tato omezení. Další informace naleznete v technických podrobnostech pro konkrétní spojnici a také [aktivační události a akce pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Název | Limit pro více klientů | Limit prostředí integrační ch služeb | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Požadavek na odchozí | 120 sekund <br>(2 minuty) | 240 sekund <br>(4 minuty) | Příklady odchozích požadavků zahrnují volání pomocí aktivačních událostí PROTOKOLU HTTP. <p><p>**Tip**: Pro delší spuštěné operace použijte [asynchronní vzorek dotazování](../logic-apps/logic-apps-create-api-app.md#async-pattern) nebo [do smyčky](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Příchozí požadavek | 120 sekund <br>(2 minuty) | 240 sekund <br>(4 minuty) | Příklady příchozích požadavků zahrnují volání přijatá aktivačními událostmi požadavku a aktivační události webhooku. <p><p>**Poznámka:** Pro původní volající získat odpověď, všechny kroky v odpovědi musí dokončit v rámci limitu, pokud zavoláte jinou aplikaci logiky jako vnořený pracovní postup. Další informace najdete v tématu [Volání, aktivační událost nebo hnízdit aplikace logiky](../logic-apps/logic-apps-http-endpoint.md). |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Velikost zpráv

| Název | Limit pro více klientů | Limit prostředí integrační ch služeb | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Velikost zpráv | 100 MB | 200 MB | Konektory s popiskem ISE používají limit ISE, nikoli limity konektorů než ISE. <p><p>Chcete-li tento limit obejít, [přečtěte si](../logic-apps/logic-apps-handle-large-messages.md)téma Zpracování velkých zpráv s bloků . Některé konektory a rozhraní API však nemusí podporovat bloků nebo dokonce výchozí limit. |
| Velikost zprávy s bloků | 1 GB | 5 GB | Toto omezení platí pro akce, které buď nativně podporují bloků nebo umožňují povolit bloků v jejich konfiguraci za běhu. <p><p>Pro prostředí služby integrace modul logic apps podporuje toto omezení, ale konektory mají své vlastní omezení bloků až do limitu motoru, například naleznete v [tématu Azure Blob Storage odkaz na rozhraní API](https://docs.microsoft.com/connectors/azureblob/). Další informace o bloků, naleznete [v tématu Zpracování velkých zpráv s bloků](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Omezení znaků

| Název | Poznámky |
|------|-------|
| Omezení pro vyhodnocení výrazu | 131 072 znaků | `@concat()`Výrazy `@base64()` `@string()` , nemohou být delší než toto omezení. |
| Požadavek na limit znaků adresy URL | 16 384 znaků |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Zásady opakování

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Opakované pokusy | 90 | Výchozí hodnota je 4. Chcete-li změnit výchozí hodnotu, použijte [parametr zásad opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximální zpoždění při opakování | 1 den | Chcete-li změnit výchozí hodnotu, použijte [parametr zásad opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimální zpoždění při opakování | 5 sekund | Chcete-li změnit výchozí hodnotu, použijte [parametr zásad opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Vlastní omezení konektoru

Tady jsou omezení pro vlastní konektory, které můžete vytvořit z webových rozhraní API.

| Název | Limit pro více klientů | Limit prostředí integrační ch služeb | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Počet vlastních konektorů | 1 000 na předplatné Azure | 1 000 na předplatné Azure ||
| Počet požadavků za minutu pro vlastní konektor | 500 požadavků za minutu na připojení | 2 000 požadavků za minutu na *vlastní konektor* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Spravované identity

| Název | Omezení |
|------|-------|
| Spravované identity podle aplikace logiky | Systémově přiřazená identita nebo 1 identita přiřazená uživatelem |
| Počet aplikací logiky, které mají spravovanou identitu v předplatném Azure podle oblasti | 250 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity integračního účtu

Každé předplatné Azure má tyto limity účtů integrace:

* Jeden účet integrace [úrovně Free](../logic-apps/logic-apps-pricing.md#integration-accounts) pro oblast Azure

* 1 000 celkových integračních účtů, včetně integračních účtů v [prostředích integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) napříč [vývojářskými i prémiovými skutami](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Každá služba ISE, ať [už vývojář nebo Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), je omezena na 5 účtů celkové integrace:

  | Skladová položka ise | Limity integračního účtu |
  |---------|----------------------------|
  | **Premium** | Celkem 5 - Pouze [standardní](../logic-apps/logic-apps-pricing.md#integration-accounts) účty, včetně jednoho standardního účtu zdarma. Nejsou povoleny žádné bezplatné nebo základní účty. |
  | **Developer** | 5 celkem - [Zdarma](../logic-apps/logic-apps-pricing.md#integration-accounts) (omezeno na 1 účet) a [standardní](../logic-apps/logic-apps-pricing.md#integration-accounts) kombinované, nebo všechny standardní účty. Nejsou povoleny žádné základní účty. Pomocí [skladové položky pro vývojáře](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) pro experimentování, vývoj a testování, ale ne pro produkční nebo testování výkonu. |
  |||

Další náklady platí pro účty integrace, které přidáte mimo účty integrace, které jsou součástí ise. Informace o tom, jak fungují ceny a fakturace pro ises, najdete v tématu [Cenové homodelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Omezení artefaktů na účet integrace

Tady jsou omezení počtu artefaktů pro každou úroveň účtu integrace.
Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o tom, jak fungují ceny a fakturace pro účty integrace, najdete v [tématu Cenový model logic apps](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Použijte free vrstvu pouze pro průzkumné scénáře, ne produkční scénáře. Tato úroveň omezuje propustnost a využití a nemá žádnou smlouvu o úrovni služeb (SLA).

| Artefakt | Free | Základní | Standard |
|----------|------|-------|----------|
| Edi obchodní dohody | 10 | 1 | 1 000 |
| Edi obchodní partneři | 25 | 2 | 1 000 |
| Maps | 25 | 500 | 1 000 |
| Schémata | 25 | 500 | 1 000 |
| Sestavení | 10 | 25 | 1 000 |
| Certifikáty | 25 | 2 | 1 000 |
| Dávkové konfigurace | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity kapacity artefaktů

| Artefakt | Omezení | Poznámky |
| -------- | ----- | ----- |
| Sestavení | 8 MB | Chcete-li nahrát soubory větší než 2 MB, použijte [účet úložiště Azure a kontejner objektů blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mapa (soubor XSLT) | 8 MB | Chcete-li nahrát soubory větší než 2 MB, použijte [rozhraní AZURE Logic Apps REST API - Mapy](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Poznámka:** Množství dat nebo záznamů, které může mapování úspěšně zpracovat, je založeno na limitech velikosti zprávy a časového limitu akce v Aplikacích Azure Logic Apps. Pokud například použijete akci HTTP založenou na [velikosti zprávy HTTP a limitech časového limitu](#request-limits), může mapování zpracovávat data až do limitu velikosti zprávy HTTP, pokud se operace dokončí v rámci časového limitu HTTP. |
| Schéma | 8 MB | Chcete-li nahrát soubory větší než 2 MB, použijte [účet úložiště Azure a kontejner objektů blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Omezení propustnosti

| Koncový bod runtime | Free | Základní | Standard | Poznámky |
|------------------|------|-------|----------|-------|
| Čtení hovorů za 5 minut | 3 000 | 30,000 | 60 000 | Podle potřeby můžete distribuovat úlohy mezi více než jeden účet. |
| Vyvolání hovorů za 5 minut | 3 000 | 30,000 | 45 000 | Podle potřeby můžete distribuovat úlohy mezi více než jeden účet. |
| Sledování hovorů za 5 minut | 3 000 | 30,000 | 45 000 | Podle potřeby můžete distribuovat úlohy mezi více než jeden účet. |
| Blokování souběžných hovorů | ~1 000 | ~1 000 | ~1 000 | Stejné pro všechny SKU. Můžete snížit počet souběžných požadavků nebo podle potřeby zkrátit dobu trvání. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Velikost zprávy protokolu B2B (AS2, X12, EDIFACT)

Zde jsou omezení velikosti zprávy, které platí pro protokoly B2B:

| Název | Limit pro více klientů | Limit prostředí integrační ch služeb | Poznámky |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 50 MB | v2 - 200 MB <br>v1 - 50 MB | Platí pro dekódování a kódování |
| X12 | 50 MB | 50 MB | Platí pro dekódování a kódování |
| EDIFACT | 50 MB | 50 MB | Platí pro dekódování a kódování |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Zakázání nebo odstranění aplikací logiky

Když zakážete aplikaci logiky, žádné nové spuštění jsou instance. Všechna probíhající a čekající spuštění pokračují až do dokončení, což může nějakou dobu trvat.

Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Konfigurace brány firewall: IP adresy a značky služeb

IP adresy, které Azure Logic Apps používá pro příchozí a odchozí volání závisí na oblasti, kde existuje vaše aplikace logiky. *Všechny* aplikace logiky ve stejné oblasti používají stejné rozsahy IP adres. Některá volání [Power Automate,](https://docs.microsoft.com/power-automate/getting-started) jako jsou požadavky **HTTP** a **HTTP + OpenAPI,** procházejí přímo službou Azure Logic Apps a pocházejí z IP adres, které jsou tady uvedené. Další informace o IP adresách používaných power automatem najdete [v tématu Limity a konfigurace v power automatu](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

> [!TIP]
> Chcete-li snížit složitost při vytváření pravidel zabezpečení, můžete volitelně použít [značky služeb](../virtual-network/service-tags-overview.md), nikoli určit IP adresy logic apps pro každou oblast, popsané dále v této části. Tyto značky fungují v oblastech, kde je služba Logic Apps dostupná:
>
> * **LogicAppsManagement**: Představuje vstupní IP adresa předpony pro službu Logic Apps.
> * **LogicApps**: Představuje předpony odchozí IP adresy pro službu Logic Apps.

* Pro [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)pevné nebo vyhrazené IP adresy nejsou k dispozici pro [vlastní konektory](../logic-apps/custom-connector-overview.md) a [spravované konektory](../connectors/apis-list.md#managed-api-connectors), například Azure Storage, SQL Server, Office 365 Outlook a tak dále.

* Chcete-li podporovat volání, která vaše aplikace logiky přímo provést pomocí [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)a další požadavky HTTP, nastavte bránu firewall se všemi [příchozí](#inbound) *a* [odchozí](#outbound) IP adresy, které jsou používány službou Logic Apps, na základě oblastí, kde existují aplikace logiky. Tyto adresy se zobrazují pod nadpisy **Příchozí** a **Odchozí** v této části a jsou seřazeny podle oblasti.

* Chcete-li podporovat volání, která [spravované konektory](../connectors/apis-list.md#managed-api-connectors) provést, nastavte bránu firewall se *všemi* [odchozí](#outbound) IP adresy používané těmito konektory, na základě oblastí, kde existují aplikace logiky. Tyto adresy se zobrazují pod nadpisem **Odchozí** v této části a jsou seřazeny podle oblasti.

* Chcete-li povolit komunikaci pro aplikace logiky, které běží v prostředí služby integrace (ISE), ujistěte se, že [otevřete tyto porty](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Pokud vaše aplikace logiky mají problémy s přístupem k účtům úložiště Azure, které používají [brány firewall a pravidla brány firewall](../storage/common/storage-network-security.md), máte různé [možnosti povolení přístupu](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Aplikace logiky například nemají přímý přístup k účtům úložiště, které používají pravidla brány firewall a existují ve stejné oblasti. Pokud však povolíte [odchozí IP adresy pro spravované konektory ve vaší oblasti](../logic-apps/logic-apps-limits-and-config.md#outbound), vaše aplikace logiky mají přístup k účtům úložiště, které se nacházejí v jiné oblasti, s výjimkou případů, kdy používáte konektory Azure Table Storage nebo Azure Queue Storage. Chcete-li získat přístup k úložišti table storage nebo front, můžete místo toho použít aktivační událost a akce protokolu HTTP. Další možnosti najdete v [tématu Přístup k účtům úložiště za bránovými firewally](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Příchozí ADRESY IP

V této části jsou uvedeny pouze příchozí IP adresy pro službu Azure Logic Apps. Chcete-li snížit složitost při vytváření pravidel zabezpečení, můžete volitelně použít [značku služby](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, spíše než zadat předpony IP adres příchozích aplikací logiky pro každou oblast. Tato značka funguje v oblastech, kde je služba Logic Apps k dispozici. Pokud máte Azure Government, přečtěte [si tématu Azure Government – příchozí IP adresy](#azure-government-inbound).

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Azure s více tenanty – příchozí IP adresy

| Oblast s více tenanty | IP adresa |
|---------------------|----|
| Austrálie – východ | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Austrálie – jihovýchod | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazílie – jih | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Střední Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada – východ | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indie – střed | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA – střed | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Východní Asie | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| USA – východ | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA – východ 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Francie – střed | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Francie – jih | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japonsko – východ | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japonsko – západ | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Jižní Korea – střed | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Jižní Korea – jih | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA – středosever | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Severní Evropa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Jižní Afrika – sever | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Jižní Afrika – západ | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA – středojih | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie – jih | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Jihovýchodní Asie | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Spojené království – jih | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Spojené království – západ | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA – středozápad | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Západní Evropa | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| Indie – západ | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA – západ | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA – západ 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government – příchozí IP adresy

| Oblast Azure Government | IP adresa |
|-------------------------|----|
| USA (Gov) – Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| USA (Gov) – Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| USA (Gov) – Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD – střed | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Odchozí IP adresy

V této části jsou uvedeny odchozí IP adresy pro službu Azure Logic Apps a spravované konektory. Chcete-li snížit složitost při vytváření pravidel zabezpečení, můžete volitelně použít [značku služby](../virtual-network/service-tags-overview.md) **LogicApps**, nikoli zadat předpony IP adres odchozích aplikací logiky pro každou oblast. Tato značka funguje v oblastech, kde je služba Logic Apps k dispozici. Pro spravované konektory použijte IP adresy. Pokud máte Azure Government, přečtěte [si tématu Azure Government – odchozí IP adresy](#azure-government-outbound).

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Azure s více tenanty – odchozí IP adresy

| Region (Oblast) | Ip aplikace logiky | Adresa IP spravované konektory |
|--------|---------------|-----------------------|
| Austrálie – východ | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Austrálie – jihovýchod | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Brazílie – jih | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Střední Kanada | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Kanada – východ | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| Indie – střed | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| USA – střed | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Východní Asie | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| USA – východ | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139 |
| USA – východ 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Francie – střed | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Francie – jih | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Japonsko – východ | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Japonsko – západ | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Jižní Korea – střed | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Jižní Korea – jih | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| USA – středosever | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Severní Evropa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Jižní Afrika – sever | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Jižní Afrika – západ | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| USA – středojih | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| Indie – jih | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Jihovýchodní Asie | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Spojené království – jih | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Spojené království – západ | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| USA – středozápad | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Západní Evropa | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| Indie – západ | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| USA – západ | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| USA – západ 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government – odchozí IP adresy

| Region (Oblast) | Ip aplikace logiky | Adresa IP spravované konektory |
|--------|---------------|-----------------------|
| USA (Gov) – Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91 |
| USA (Gov) – Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225 |
| USA (Gov) – Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
| US DoD – střed | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136 |
||||

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Informace o [běžných příkladech a scénářích](../logic-apps/logic-apps-examples-and-scenarios.md)
