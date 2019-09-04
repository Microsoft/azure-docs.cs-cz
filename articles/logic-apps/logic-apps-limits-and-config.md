---
title: Omezení a konfigurace-Azure Logic Apps | Microsoft Docs
description: Omezení služby a hodnoty konfigurace pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: 401b33c28e4ba91a0da5e4ab38f920e173302ea1
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242374"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Omezení a informace o konfiguraci Azure Logic Apps

Tento článek popisuje omezení a podrobnosti konfigurace pro vytváření a spouštění automatizovaných pracovních postupů pomocí Azure Logic Apps. Microsoft Flow najdete v části [omezení a konfigurace v Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Omezení definice

Tady jsou omezení pro jednu definici aplikace logiky:

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce na pracovní postup | 500 | Pro prodloužení tohoto limitu můžete podle potřeby přidávat vnořené pracovní postupy. |
| Povolená hloubka vnořování pro akce | 8 | Pro prodloužení tohoto limitu můžete podle potřeby přidávat vnořené pracovní postupy. |
| Pracovní postupy na oblast a předplatné | 1 000 | |
| Triggery na pracovní postup | 10 | Při práci v zobrazení kódu, nikoli v Návrháři |
| Omezení případů rozsahu přepnutí | 25 | |
| Proměnné na pracovní postup | 250 | |
| Počet znaků na výraz | 8 192 | |
| Maximální velikost pro`trackedProperties` | 16 000 znaků |
| `action` Název nebo`trigger` | 80 znaků | |
| Délka`description` | 256 znaků | |
| Velikosti`parameters` | 50 | |
| Velikosti`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Doba trvání běhu a omezení uchování

Tady jsou omezení pro jeden běh aplikace logiky:

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Doba trvání běhu | 90 dní | 365 dní | Pokud chcete změnit výchozí limit, přečtěte si téma [Změna doby trvání běhu](#change-duration). |
| Uchování úložiště | 90 dní od času spuštění | 365 dní | Pokud chcete změnit výchozí limit, přečtěte si téma [Změna uchovávání úložiště](#change-retention). |
| Minimální interval opakování | 1 sekunda | 1 sekunda ||
| Maximální interval opakování | 500 dní | 500 dní ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Změna doby trvání běhu a uchování úložiště

Pokud chcete změnit výchozí limit doby trvání běhu a uchování úložiště, postupujte podle těchto kroků. Pokud potřebujete přejít nad maximální limit, [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) , kde vám pomůžou vaše požadavky.

1. V Azure Portal nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

2. V části **Možnosti modulu runtime**v seznamu **uchování historie spuštění v části dny** vyberte možnost **vlastní**.

3. Zadejte nebo přetáhněte posuvník počtu dní, které chcete.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Omezení souběžnosti, smyček a dedávkování

Tady jsou omezení pro jeden běh aplikace logiky:

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Souběžnost triggeru | * Neomezeno, pokud je řízení souběžnosti vypnuto <p><p>* 25 je výchozím limitem při zapnutí řízení souběžnosti, které nelze vrátit zpět po zapnutí ovládacího prvku. Výchozí hodnotu můžete změnit na hodnotu v rozmezí 1 až 50 (včetně). | Toto omezení popisuje nejvyšší počet instancí aplikace logiky, které mohou běžet současně, nebo paralelně. <p><p>Pokud chcete změnit výchozí limit na hodnotu v rozmezí 1 až 50 (včetně), přečtěte si téma [Změna limitu souběžnosti triggeru](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) nebo [instancí triggerů postupně](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximální počet čekajících spuštění | Když je řízení souběžnosti zapnuto, minimální počet čekajících spuštění je 10 plus počet souběžných spuštění (aktivační souběžnost). Maximální počet můžete změnit až na 100 včetně. | Toto omezení popisuje nejvyšší počet instancí aplikace logiky, které mohou čekat na spuštění, když aplikace logiky již používá maximální počet souběžných instancí. <p><p>Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Položky pole foreach | 100,000 | Toto omezení popisuje nejvyšší počet položek pole, které může smyčka for each zpracovat. <p><p>Chcete-li filtrovat větší pole, můžete použít [akci dotazu](../connectors/connectors-native-query.md). |
| Souběžnost foreach | 20 je výchozím limitem při vypnutém řízení souběžnosti. Výchozí hodnotu můžete změnit na hodnotu v rozmezí 1 až 50 (včetně). | Toto omezení je nejvyšší počet iterací smyčky for each, které lze spustit současně nebo paralelně. <p><p>Chcete-li změnit výchozí limit na hodnotu mezi 1 a 50, přečtěte si téma [Změna "pro každé" omezení souběžnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) nebo [spuštění každé smyčky "pro každou" cyklicky](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| SplitOn položky | 100,000 | U triggerů, které vracejí pole, můžete zadat výraz, který používá vlastnost SplitOn, která [rozdělí nebo oddělí dávky polí pole do více instancí pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) pro zpracování namísto použití smyčky "foreach". Tento výraz odkazuje na pole, které se má použít pro vytvoření a spuštění instance pracovního postupu pro každou položku pole. |
| Do iterací | 5,000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Omezení propustnosti

Tady jsou omezení pro jednu definici aplikace logiky:

### <a name="multi-tenant-logic-apps-service"></a>Služba Logic Apps pro více tenantů

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce: Spuštění za 5 minut | 100 000 je výchozí limit, ale 300 000 je maximální limit. | Pokud chcete změnit výchozí limit, přečtěte si téma [spuštění aplikace logiky v režimu vysoké propustnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), který je ve verzi Preview. Případně můžete distribuovat úlohy napříč více aplikacemi logiky podle potřeby. |
| Akce: Souběžná odchozí volání | ~2,500 | Můžete snížit počet souběžných požadavků nebo snížit dobu trvání podle potřeby. |
| Koncový bod modulu runtime: Souběžná příchozí volání | ~1,000 | Můžete snížit počet souběžných požadavků nebo snížit dobu trvání podle potřeby. |
| Koncový bod modulu runtime: Čtení hovorů za 5 minut  | 60,000 | V případě potřeby můžete distribuovat úlohy napříč více než jednou aplikací. |
| Koncový bod modulu runtime: Vyvolat volání za 5 minut | 45,000 | V případě potřeby můžete distribuovat úlohy napříč více než jednou aplikací. |
| Propustnost obsahu za 5 minut | 600 MB | V případě potřeby můžete distribuovat úlohy napříč více než jednou aplikací. |
||||

### <a name="integration-service-environment-ise"></a>Prostředí služby Integration Service (ISE)

Tady jsou limity propustnosti pro SKU úrovně Premium:

| Name | Omezení | Poznámky |
|------|-------|-------|
| Omezení spuštění základní jednotky | Omezené na systém, když kapacita infrastruktury dosáhne 80% | Poskytuje operace ~ 4 000 Actions za minutu, což je ~ 160 000 000 provádění akcí za měsíc. | |
| Limit spouštění jednotek škálování | Omezené na systém, když kapacita infrastruktury dosáhne 80% | Každá jednotka škálování může poskytnout ~ 2 000 další provádění akcí za minutu, což znamená ~ 80 000 000 další provádění akcí za měsíc. | |
| Maximální počet jednotek škálování, které můžete přidat | 10 | |
||||

Chcete-li přejít nad tato omezení v normálním zpracování nebo spustit testování zatížení, které by mohlo jít nad tato omezení, [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) , kde vám pomohou vaše požadavky.

> [!NOTE]
> [SKU vývojáře](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) nemá žádná publikovaná omezení, protože tato SKU nemá žádnou smlouvu o úrovni služeb (SLA) ani možnosti škálování. Tuto SKU použijte pouze pro experimentování, vývoj a testování, nikoli pro produkční nebo výkonnostní testování.

<a name="request-limits"></a>

## <a name="http-limits"></a>Omezení HTTP

Tady jsou omezení pro jeden požadavek HTTP nebo synchronní volání konektoru:

#### <a name="timeout"></a>časový limit

Některé operace konektoru provádějí asynchronní volání nebo naslouchání požadavkům Webhooku, takže časový limit těchto operací může být delší než tato omezení. Další informace najdete v technických podrobnostech ke konkrétnímu konektoru a také [triggery a akce pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Odchozí požadavek | 120 sekund | 240 sekund | Pro delší běžící operace použijte [asynchronní vzorek cyklického dotazování](../logic-apps/logic-apps-create-api-app.md#async-pattern) nebo [do smyčky do](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Synchronní odpověď | 120 sekund | 240 sekund | Pro původní požadavek na získání odpovědi musí být všechny kroky v odpovědi dokončené v rámci limitu, pokud nebudete volat jinou aplikaci logiky jako vnořený pracovní postup. Další informace najdete v tématu [volání, Trigger nebo vnořování Logic Apps](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Velikost zpráv

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Velikost zpráv | 100 MB | 200 MB | Pokud chcete tento limit obejít, přečtěte si téma [zpracování velkých zpráv pomocí bloků dat](../logic-apps/logic-apps-handle-large-messages.md). Některé konektory a rozhraní API ale nemusí podporovat blokování nebo ani výchozí omezení. |
| Velikost zprávy pomocí bloků dat | 1 GB | 5 GB | Toto omezení se vztahuje na akce, které nativně podporují dělení na bloky dat, nebo umožňují povolit v konfiguraci modulu runtime vytváření bloků dat. <p>Pro prostředí integrační služby podporuje modul Logic Apps tento limit, ale konektory mají vlastní meze omezení na modul, například v tématu [Azure Blob Storage Connector](/connectors/azureblob/). Další informace najdete v tématu [zpracování velkých zpráv pomocí bloků dat](../logic-apps/logic-apps-handle-large-messages.md). |
| Omezení vyhodnocení výrazu | 131 072 znaků | 131 072 znaků | Výrazy `@concat()`, `@base64()` a`@string()` nemohou být delší než tento limit. |
|||||

#### <a name="retry-policy"></a>Zásady opakování

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Opakované pokusy | 90 | Výchozí hodnota je 4. Chcete-li změnit výchozí nastavení, použijte [parametr zásady opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximální zpoždění opakování | 1 den | Chcete-li změnit výchozí nastavení, použijte [parametr zásady opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimální zpoždění opakování | 5 sekund | Chcete-li změnit výchozí nastavení, použijte [parametr zásady opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Omezení vlastního konektoru

Tady jsou limity pro vlastní konektory, které můžete vytvořit z webových rozhraní API.

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Počet vlastních konektorů | 1 000 na předplatné Azure | 1 000 na předplatné Azure ||
| Počet požadavků za minutu pro vlastní konektor | 500 požadavků za minutu na připojení | 2 000 požadavků za minutu na *vlastní konektor* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Spravované identity

| Name | Omezení |
| ---- | ----- |
| Počet aplikací logiky se spravovanými identitami přiřazenými systémem na předplatné Azure | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Omezení účtu pro integraci

Každé předplatné Azure má tento účet pro integraci:

* Jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-pricing.md#integration-accounts) na oblast Azure

* 1 000 Celkový počet integračních účtů, včetně integračních účtů v jakémkoli [prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) napříč [SKU pro vývojáře a úrovně Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Každý ISE, ať už [vývojář nebo Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), je omezený na 5 celkových integračních účtů:

  | SKU ISE | Omezení účtu pro integraci |
  |---------|----------------------------|
  | **Premium** | 5 Total – pouze [standardní](../logic-apps/logic-apps-pricing.md#integration-accounts) účty, včetně jednoho standardního účtu zdarma. Nejsou povoleny žádné bezplatné nebo základní účty. |
  | **Developer** | 5 [bez](../logic-apps/logic-apps-pricing.md#integration-accounts) součtu (s omezením na 1 účet [) a kombinovaný nebo všechny standardní účty](../logic-apps/logic-apps-pricing.md#integration-accounts) . Nejsou povoleny žádné základní účty. Použijte [SKU pro vývojáře](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) pro experimentování, vývoj a testování, ale ne pro produkční nebo výkonnostní testování. |
  |||

Další náklady se vztahují na účty pro integraci, které přidáte nad rámec účtů pro integraci, které jsou součástí ISE. Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Omezení počtu artefaktů na účet pro integraci

Tady jsou limity pro počet artefaktů pro každou vrstvu integračního účtu. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o cenách a fakturační práci pro účty pro integraci najdete v [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Úroveň Free se používá jenom pro scénáře průzkumného prostředí, nikoli pro produkční scénáře. Tato úroveň omezuje propustnost a využití a nemá smlouvu o úrovni služeb (SLA).

| Artefakt | Zdarma | Basic | Standard |
|----------|------|-------|----------|
| Obchodní smlouvy EDI | 10 | 1 | 1 000 |
| Obchodní partneři EDI | 25 | 2 | 1 000 |
| Maps | 25 | 500 | 1 000 |
| Schémata | 25 | 500 | 1 000 |
| Sestavení | 10 | 25 | 1 000 |
| Certifikáty | 25 | 2 | 1 000 |
| Konfigurace dávek | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Omezení kapacity artefaktů

| Artefakt | Omezení | Poznámky |
| -------- | ----- | ----- |
| Sestavení | 8 MB | Pokud chcete nahrávat soubory větší než 2 MB, použijte [účet úložiště Azure a kontejner objektů BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (soubor XSLT) | 8 MB | Chcete-li odeslat soubory větší než 2 MB, použijte [mapování Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schéma | 8 MB | Pokud chcete nahrávat soubory větší než 2 MB, použijte [účet úložiště Azure a kontejner objektů BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Koncový bod modulu runtime | Omezení | Poznámky |
|------------------|-------|-------|
| Čtení hovorů za 5 minut | 60,000 | V případě potřeby můžete úlohy distribuovat napříč více než jedním účtem. |
| Vyvolat volání za 5 minut | 45,000 | V případě potřeby můžete úlohy distribuovat napříč více než jedním účtem. |
| Sledování hovorů za 5 minut | 45,000 | V případě potřeby můžete úlohy distribuovat napříč více než jedním účtem. |
| Blokování souběžných volání | ~1,000 | Můžete snížit počet souběžných požadavků nebo snížit dobu trvání podle potřeby. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Velikost zprávy protokolu B2B (AS2, X12, EDIFACT)

Tady jsou omezení velikosti zpráv, která se vztahují na protokoly B2B:

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>V1 – 50 MB | V2 – 200 MB <br>V1 – 50 MB | Platí pro dekódování a kódování. |
| X12 | 50 MB | 50 MB | Platí pro dekódování a kódování. |
| EDIFACT | 50 MB | 50 MB | Platí pro dekódování a kódování. |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Zakázání nebo odstranění Logic Apps

Když aplikaci logiky zakážete, nevytvoří se žádná nová spuštění. Všechny probíhající a nedokončené běhy pokračují, dokud se nedokončí, což může chvíli trvat.

Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Konfigurace brány firewall: IP adresy

Všechny Logic Apps ve stejné oblasti používají stejné rozsahy IP adres. Aby bylo možné podporovat volání, která vaše aplikace logiky přímo provádí pomocí [http](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)a dalších požadavků HTTP, nastavte brány firewall se *všemi* [příchozími](#inbound) *a* [odchozími](#outbound) IP adresami, které používá služba Logic Apps. , a to na základě oblastí, kde existují aplikace logiky. Tyto adresy se zobrazí pod položkami **příchozí** a **odchozí** v této části a jsou seřazené podle oblasti. 

Aby bylo možné podporovat volání, která vytváří [konektory spravované Microsoftem](../connectors/apis-list.md) , nastavte bránu firewall se *všemi* [odchozími](#outbound) IP adresami používanými těmito konektory na základě oblastí, ve kterých existují vaše aplikace logiky. Tyto adresy se zobrazí pod **výstupní** hlavičkou v této části a jsou seřazené podle oblasti. U aplikací logiky, které běží v prostředí ISE (Integration Service Environment), nezapomeňte [tyto porty otevřít](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#ports).

Pro vlastní konektory, [Azure Government](../azure-government/documentation-government-overview.md)a [Azure Čína 21Vianet](https://docs.microsoft.com/azure/china/)nejsou k dispozici pevné nebo rezervované IP adresy.

> [!IMPORTANT]
>
> Pokud máte existující konfigurace, aktualizujte je **co nejdříve do 1. září 2018** tak, aby obsahovaly a odpovídaly IP adresám v těchto seznamech pro oblasti, ve kterých existují aplikace logiky.

Logic Apps nepodporuje přímé připojení k účtům úložiště Azure přes brány firewall. Pokud chcete získat přístup k těmto účtům úložiště, použijte jednu z možností:

* Vytvořte [prostředí integrační služby](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), které se může připojit k prostředkům ve službě Azure Virtual Network.

* Pokud již používáte API Management, můžete použít tuto službu pro tento scénář. Další informace najdete v tématu [Jednoduchá architektura podnikové integrace](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Příchozí IP adresy – jenom Logic Apps služby

| Oblast | IP |
|--------|----|
| Austrálie – východ | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Austrálie – jihovýchod | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazílie – jih | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Kanada – střed | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kanada – východ | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Střed Indie | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Střed USA | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Východní Asie | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| East US | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Východní USA 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japonsko – východ | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japonsko – západ | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Střed USA – sever | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Severní Evropa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Střed USA – jih | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Jižní Indie | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Jihovýchodní Asie | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Západní střed USA | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Západní Evropa | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Indie – západ | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| USA – západ | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| USA – západ 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Velká Británie – jih | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Spojené království – západ | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Odchozí IP adresy – služba Logic Apps & spravované konektory

| Oblast | Logic Apps IP | IP adresa spravovaného konektoru |
|--------|---------------|-----------------------|
| Austrálie – východ | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10 |
| Austrálie – jihovýchod | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.77.50.240 - 13.77.50.255, 13.70.136.174 |
| Brazílie – jih | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 191.233.203.192 - 191.233.203.207, 104.41.59.51 | 
| Kanada – střed | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126 |
| Kanada – východ | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.242.35.152 |
| Střed Indie | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 104.211.81.192 - 104.211.81.207, 52.172.211.12 |
| Střed USA | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 52.173.245.164 |
| Východní Asie | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 52.175.23.169 |
| East US | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| Východní USA 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154 |
| Japonsko – východ | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.78.108.0 - 13.78.108.15, 13.71.153.19 |
| Japonsko – západ | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 104.215.61.248 |
| Střed USA – sever | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161 |
| Severní Evropa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68 |
| Střed USA – jih | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 104.214.19.48 - 104.214.19.63, 13.65.86.57 |
| Jižní Indie | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 40.78.194.240 - 40.78.194.255, 13.71.125.22 |
| Jihovýchodní Asie | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 52.187.68.19 |
| Západní střed USA | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.102.22 |
| Západní Evropa | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 52.174.88.118 |
| Indie – západ | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.189.218 |
| USA – západ | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 | 40.112.243.160 - 40.112.243.175, 104.42.122.49 |
| USA – západ 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 52.183.78.157 |
| Velká Británie – jih | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.148.0 - 51.140.148.15, 51.140.80.51 |
| Spojené království – západ | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Další postup  

* Naučte se [vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) .  
* Další informace o [běžných příkladech a scénářích](../logic-apps/logic-apps-examples-and-scenarios.md)
