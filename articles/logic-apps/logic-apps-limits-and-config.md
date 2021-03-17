---
title: Omezení a konfigurace
description: Omezení služeb, jako je doba trvání, propustnost a kapacita, plus hodnoty konfigurace, jako jsou například IP adresy, které mají být povoleny, pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 03/18/2021
ms.openlocfilehash: f4336350af92c27760369d668c6babddc4d4ea30
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462912"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informace o omezeních a konfiguraci pro Azure Logic Apps

Tento článek popisuje omezení a podrobnosti konfigurace pro vytváření a spouštění automatizovaných pracovních postupů pomocí Azure Logic Apps. Informace o Power Automate najdete [v tématu omezení a konfigurace v Power](/flow/limits-and-config)Automate.

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Omezení definice aplikace logiky

Tady jsou omezení pro jednu definici aplikace logiky:

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce na pracovní postup | 500 | Pro prodloužení tohoto limitu můžete podle potřeby přidávat vnořené pracovní postupy. |
| Povolená hloubka vnořování pro akce | 8 | Pro prodloužení tohoto limitu můžete podle potřeby přidávat vnořené pracovní postupy. |
| Pracovní postupy na oblast a předplatné | 1 000 | |
| Triggery na pracovní postup | 10 | Při práci v zobrazení kódu, nikoli v Návrháři |
| Omezení případů rozsahu přepnutí | 25 | |
| Proměnné na pracovní postup | 250 | |
| Název `action` nebo `trigger` | 80 znaků | |
| Počet znaků na výraz | 8 192 | |
| Délka `description` | 256 znaků | |
| Maximální počet `parameters` | 50 | |
| Maximální počet `outputs` | 10 | |
| Maximální velikost pro `trackedProperties` | 16 000 znaků |
| Akce vloženého kódu – maximální počet znaků kódu | 1 024 znaků | Pokud chcete tento limit prodloužit na 100 000 znaků, vytvořte aplikace logiky pomocí typu prostředku **Logic App (Preview)** , a to buď [pomocí Azure Portal](create-stateful-stateless-workflows-azure-portal.md) , nebo [pomocí Visual Studio Code a rozšíření **Azure Logic Apps (Preview)**](create-stateful-stateless-workflows-visual-studio-code.md). |
| Akce vloženého kódu – maximální doba trvání spuštění kódu | 5 sekund | Pokud chcete tento limit prodloužit na 15 sekund, vytvořte aplikace logiky pomocí typu prostředku **Logic App (Preview)** , a to buď [pomocí Azure Portal](create-stateful-stateless-workflows-azure-portal.md) , nebo [pomocí Visual Studio Code a rozšíření **Azure Logic Apps (Preview)**](create-stateful-stateless-workflows-visual-studio-code.md). |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Omezení doby trvání běhu a historie uchovávání

Tady jsou omezení pro jeden běh aplikace logiky:

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Doba trvání spuštění | 90 dnů | 366 dní | Doba trvání běhu se počítá pomocí počátečního času běhu a limitu zadaného v nastavení pracovního postupu, [**uchování historie spuštění ve dnech v**](#change-duration) čase spuštění. <p><p>Pokud chcete změnit výchozí limit, přečtěte si téma [Změna doby trvání běhu a uchování historie v úložišti](#change-duration). |
| Uchování historie spuštění v úložišti | 90 dnů | 366 dní | Pokud doba trvání běhu překročí aktuální limit uchování historie spuštění, odebere se běh z historie spuštění v úložišti. Bez ohledu na to, jestli je běh dokončený nebo časový limit, se uchování historie spuštění vždycky počítá pomocí počátečního času spuštění a aktuálního limitu zadaného v nastavení pracovního postupu, [**uchování historie spuštění ve dnech**](#change-retention). Bez ohledu na předchozí omezení se aktuální limit vždycky používá pro výpočet uchovávání. <p><p>Pokud chcete změnit výchozí limit a další informace, přečtěte si téma [Změna doby trvání a uchování historie spuštění v úložišti](#change-retention). Chcete-li zvýšit maximální limit, [obraťte se na tým Logic Apps](mailto://logicappspm@microsoft.com) , kde vám pomůžou vaše požadavky. |
| Minimální interval opakování | 1 sekunda | 1 sekunda ||
| Maximální interval opakování | 500 dnů | 500 dnů ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Změna doby trvání běhu a uchování historie v úložišti

Stejné nastavení řídí maximální počet dní, po které může pracovní postup běžet, a pro uchování historie spuštění v úložišti. Chcete-li změnit výchozí nebo aktuální limit těchto vlastností, postupujte podle těchto kroků.

* Pro Logic Apps ve více tenantů Azure je výchozí limit 90 dne stejný jako maximální limit. Tuto hodnotu lze snížit pouze.

* U Logic Apps v prostředí integrační služby můžete snížit nebo zvýšit výchozí limit 90.

Předpokládejme například, že omezíte dobu uchovávání dat z 90 dnů na 30 dní. Z historie spuštění se odebere 60. den a předchozí spuštění. Pokud zvýšíte dobu uchovávání z 30 dnů na 60 dní, pak 20. staré spuštění zůstane v historii spuštění dalších 40 dnů.

> [!IMPORTANT]
> Pokud doba trvání běhu překročí aktuální limit uchování historie spuštění, odebere se běh z historie spuštění v úložišti. Aby nedošlo ke ztrátě historie spuštění, ujistěte se, že limit uchování je *vždycky* větší než nejdelší možná doba trvání běhu.

1. V poli hledání [Azure Portal](https://portal.azure.com) vyhledejte a vyberte **Aplikace logiky**.

1. Najděte a vyberte svou aplikaci logiky. Otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

1. V části **Možnosti modulu runtime** v seznamu **uchování historie spuštění ve dnech** vyberte možnost **vlastní**.

1. Přetažením posuvníku změníte počet dní, které chcete.

1. Až skončíte, na panelu nástrojů **Nastavení pracovního postupu** vyberte **Uložit**.

Pokud vygenerujete šablonu Azure Resource Manager pro vaši aplikaci logiky, toto nastavení se zobrazí jako vlastnost v definici prostředků pracovního postupu, která je popsána v [referenčních informacích k šabloně pracovních postupů Microsoft. Logic](/azure/templates/microsoft.logic/workflows):

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Omezení souběžnosti, smyček a dedávkování

Tady jsou omezení pro jeden běh aplikace logiky:

### <a name="loops"></a>Smyčky

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Položky pole foreach | 100 000 | Toto omezení popisuje maximální počet položek pole, které může smyčka for each zpracovat. <p><p>Chcete-li filtrovat větší pole, můžete použít [akci dotazu](logic-apps-perform-data-operations.md#filter-array-action). |
| Souběžnost foreach | Při vypnuté souběžnosti: 20 <p><p>S Concurrency on: <p><p>-Výchozí: 20 <br>-Minimum: 1 <br>-Max: 50 | Toto omezení je maximální počet iterací smyčky for each, které lze spustit současně nebo paralelně. <p><p>Pokud chcete tento limit změnit, přečtěte si téma [Změna "pro každé" omezení souběžnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) nebo [spuštění každé smyčky "pro každou" cyklicky](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Do iterací | -Výchozí: 60 <br>-Minimum: 1 <br>-Max: 5 000 | Maximální počet cyklů, které může mít smyčka "do" při spuštění aplikace logiky. <p><p>Chcete-li tento limit změnit, vyberte u obrazce smyčka "do" možnost **změnit omezení** a zadejte hodnotu vlastnosti **Count** . |
| Do vypršení časového limitu | -Výchozí: PT1H (1 hodina) | Maximální doba, po kterou může smyčka "do" běžet před ukončením a je zadána ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Hodnota časového limitu se vyhodnocuje pro každý cyklus smyčky. Pokud akce ve smyčce trvá déle, než je časový limit, aktuální cyklus se nezastaví. Další cyklus se ale nespustí, protože podmínka limitu není splněná. <p><p>Chcete-li tento limit změnit, vyberte u obrazce smyčka "do" možnost **změnit omezení** a zadejte hodnotu pro vlastnost **timeout** . |
||||

### <a name="concurrency-and-debatching"></a>Souběžné a dedávkování

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Souběžnost triggeru | Při vypnuté souběžnosti: neomezené <p><p>Se zapnutou souběžnou operací, kterou nelze vrátit zpět po povolení: <p><p>-Výchozí: 25 <br>-Minimum: 1 <br>-Max: 50 | Toto omezení je maximální počet instancí aplikace logiky, které mohou běžet současně, nebo paralelně. <p><p>**Poznámka**: při zapnuté souběžnosti se limit SplitOn snižuje na 100 položek pro [oddávkování polí](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Chcete-li tento limit změnit, přečtěte si téma [Změna limitu souběžnosti triggeru](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) nebo [instancí triggerů postupně](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximální počet čekajících spuštění | Při vypnuté souběžnosti: <p><p>-Minimum: 1 <br>-Max: 50 <p><p>S Concurrency on: <p><p>-Min: 10 plus počet souběžných spuštění (aktivační Concurrency) <br>-Max: 100 | Toto omezení je maximální počet instancí aplikace logiky, které mohou čekat na spuštění, když aplikace logiky již používá maximální počet souběžných instancí. <p><p>Pokud chcete tento limit změnit, přečtěte si téma [Změna limitu čekání na spuštění](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Položky SplitOn | Při vypnuté souběžnosti: 100 000 <p><p>S Concurrency na: 100 | U triggerů, které vracejí pole, můžete zadat výraz, který používá vlastnost SplitOn, která [rozdělí nebo oddělí dávky polí pole do více instancí pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) pro zpracování namísto použití smyčky "foreach". Tento výraz odkazuje na pole, které se má použít pro vytvoření a spuštění instance pracovního postupu pro každou položku pole. <p><p>**Poznámka**: když je souběžnost zapnutá, limit SplitOn se sníží na 100 položek. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Omezení propustnosti

Tady jsou omezení pro jednu definici aplikace logiky:

### <a name="multi-tenant-logic-apps-service"></a>Služba Logic Apps pro více tenantů

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce: počet spuštění na 5 minut – Kumulovaný interval | -100 000 spuštění (výchozí) <p><p>-300 000 spuštění (maximum v režimu vysoké propustnosti)  | Pokud chcete pro vaši aplikaci logiky zvýšit výchozí omezení na maximální limit, přečtěte si téma [spuštění v režimu vysoké propustnosti](#run-high-throughput-mode), který je ve verzi Preview. Případně můžete [distribuovat úlohy napříč více aplikacemi logiky](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) podle potřeby. |
| Action: souběžná odchozí volání | ~2500 | Můžete snížit počet souběžných požadavků nebo snížit dobu trvání podle potřeby. |
| Koncový bod za běhu: souběžná příchozí volání | ~ 1 000 | Můžete snížit počet souběžných požadavků nebo snížit dobu trvání podle potřeby. |
| Koncový bod za běhu: čtení hovorů za 5 minut  | 60 000 | Toto omezení platí pro volání, která získávají nezpracované vstupy a výstupy z historie spuštění aplikace logiky. V případě potřeby můžete úlohy distribuovat napříč více než jednou aplikací. |
| Koncový bod Runtime: vyvolání volání za 5 minut | 45 000 | V případě potřeby můžete distribuovat úlohy napříč více než jednou aplikací. |
| Propustnost obsahu za 5 minut | 600 MB | V případě potřeby můžete distribuovat úlohy napříč více než jednou aplikací. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Spustit v režimu vysoké propustnosti

Pro jednu definici aplikace logiky má počet akcí, které se spustí každých 5 minut, [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chcete-li zvýšit výchozí omezení na [maximální limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) pro vaši aplikaci logiky, což je třikrát výchozí omezení, můžete povolit režim vysoké propustnosti, který je ve verzi Preview. Případně můžete [distribuovat úlohy napříč více aplikacemi logiky](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) podle potřeby.

1. V Azure Portal v nabídce aplikace logiky v části **Nastavení** vyberte **Nastavení pracovního postupu**.

1. V části **Možnosti běhu**  >  s **vysokou propustností** změňte nastavení na **zapnuto**.

   ![Snímek obrazovky, který zobrazuje nabídku aplikace logiky v Azure Portal s "nastavením pracovního postupu" a vysokou propustností "nastavenou na" on ".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Pokud chcete toto nastavení povolit v šabloně ARM pro nasazení aplikace logiky, přidejte do `properties` objektu pro definici prostředků vaší aplikace logiky `runtimeConfiguration` objekt s `operationOptions` vlastností nastavenou na `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Další informace o definici prostředků aplikace logiky najdete v tématu [Přehled: Automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

### <a name="integration-service-environment-ise"></a>Prostředí služby Integration Service (ISE)

* [ISE SKU pro vývojáře](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): poskytuje až 500 spuštění za minutu, ale pamatujte na tyto požadavky:

  * Ujistěte se, že jste tuto SKLADOVOU položku používali jenom pro zkoumání, experimenty, vývoj nebo testování – ne pro produkční nebo výkonnostní testování. Tato SKU nemá žádnou smlouvu o úrovni služeb (SLA), schopnost horizontálního navýšení kapacity ani redundanci při recyklaci, což znamená, že se může vyskytnout zpoždění nebo výpadek.

  * Aktualizace back-endu můžou službu obcházet bez výpadků.

* [ISE SKU úrovně Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): Následující tabulka popisuje omezení propustnosti SKU, ale pokud chcete tyto limity překročit v normálním zpracování, nebo spustit testování zatížení, které by mohlo jít nad tato omezení, [požádejte tým Logic Apps](mailto://logicappsemail@microsoft.com) , aby vám pomohli s vašimi požadavky.

  | Name | Omezení | Poznámky |
  |------|-------|-------|
  | Omezení spuštění základní jednotky | Omezené na systém, když kapacita infrastruktury dosáhne 80% | Poskytuje operace ~ 4 000 Actions za minutu, což je ~ 160 000 000 provádění akcí za měsíc. |
  | Limit spouštění jednotek škálování | Omezené na systém, když kapacita infrastruktury dosáhne 80% | Každá jednotka škálování může poskytnout ~ 2 000 další provádění akcí za minutu, což znamená ~ 80 000 000 další provádění akcí za měsíc. |
  | Maximální počet jednotek škálování, které můžete přidat | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limity brány

Azure Logic Apps podporuje přes bránu operace zápisu včetně vkládání a aktualizace. Nicméně tyto operace mají [omezení velikosti datové části](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="http-limits"></a>

## <a name="http-limits"></a>Limity HTTP

Tady jsou omezení pro jedno příchozí nebo odchozí volání:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Doba trvání časového limitu

Některé operace konektoru provádějí asynchronní volání nebo naslouchání požadavkům Webhooku, takže časový limit těchto operací může být delší než tato omezení. Další informace najdete v technických podrobnostech ke konkrétnímu konektoru a také [triggery a akce pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Logic Apps (více tenantů) | Logic Apps (Preview) | Prostředí integrační služby | Poznámky |
|------|---------------------------|----------------------|---------------------------------|-------|
| Odchozí požadavek | 120 sekund <br>(2 minuty) | 230 sekund <br>(3,9 min.) | 240 sekund <br>(4 minuty) | Příklady odchozích požadavků zahrnují volání prováděná triggerem nebo akcí HTTP. Další informace o verzi Preview najdete v tématu [Azure Logic Apps Preview](logic-apps-overview-preview.md). <p><p>**Tip**: pro delší běžící operace použijte [asynchronní vzorek cyklického dotazování](../logic-apps/logic-apps-create-api-app.md#async-pattern) nebo [do smyčky do](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Pokud chcete při volání jiné aplikace logiky, která má k disvolanému [koncovému bodu](logic-apps-http-endpoint.md), obejít časový limit, můžete místo toho použít integrovanou Azure Logic Apps akci, kterou můžete najít ve výběru konektoru v části **integrovaná**. |
| Příchozí žádost | 120 sekund <br>(2 minuty) | 230 sekund <br>(3,9 min.) | 240 sekund <br>(4 minuty) | Příklady příchozích požadavků zahrnují volání přijatá triggerem požadavku, Trigger Webhooku protokolu HTTP a akci Webhooku protokolu HTTP. Další informace o verzi Preview najdete v tématu [Azure Logic Apps Preview](logic-apps-overview-preview.md). <p><p>**Poznámka**: původnímu volajícímu získá odpověď, všechny kroky v odpovědi musí být dokončeny v rámci limitu, pokud nebudete volat jinou aplikaci logiky jako vnořený pracovní postup. Další informace najdete v tématu [volání, Trigger nebo vnořování Logic Apps](../logic-apps/logic-apps-http-endpoint.md). |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Velikost zpráv

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Velikost zpráv | 100 MB | 200 MB | Pokud chcete tento limit obejít, přečtěte si téma [zpracování velkých zpráv pomocí bloků dat](../logic-apps/logic-apps-handle-large-messages.md). Některé konektory a rozhraní API ale nemusí podporovat blokování nebo ani výchozí omezení. <p><p>– Konektory, jako jsou AS2, X12 a EDIFACT, mají vlastní [omezení zpráv B2B](#b2b-protocol-limits). <br>-Konektory ISE používají limit ISE, ne jejich omezení konektoru ISE. |
| Velikost zprávy pomocí bloků dat | 1 GB | 5 GB | Toto omezení se vztahuje na akce, které nativně podporují vytváření bloků dat, nebo umožňují povolit v konfiguraci modulu runtime vytváření bloků dat. <p><p>Pokud používáte ISE, Logic Apps modul tento limit podporuje, ale konektory mají vlastní meze omezení na modul, například v [referenčních informacích k rozhraní API konektoru Azure Blob Storage](/connectors/azureblob/). Další informace o dělení na bloky dat najdete v tématu [zpracování velkých zpráv pomocí bloků dat](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Omezení znaků

| Name | Omezení | Poznámky |
|------|-------|-------|
| Omezení pro vyhodnocení výrazu | 131 072 znaků | `@concat()`Výrazy, `@base64()` a `@string()` nemohou být delší než tento limit. |
| Omezení počtu znaků adresy URL žádosti | 16 384 znaků | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Zásady opakování

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Opakované pokusy | 90 | Výchozí hodnota je 4. Chcete-li změnit výchozí nastavení, použijte [parametr zásady opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximální zpoždění při opakování | 1 den | Chcete-li změnit výchozí nastavení, použijte [parametr zásady opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimální zpoždění při opakování | 5 sekund | Chcete-li změnit výchozí nastavení, použijte [parametr zásady opakování](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Omezení ověřování

Tady jsou omezení pro aplikaci logiky, která začíná triggerem žádosti a povoluje [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) pro autorizaci příchozích volání triggeru požadavku:

| Name | Omezení | Poznámky |
| ---- | ----- | ----- |
| Zásady autorizace Azure AD | 5 | |
| Deklarace identity na zásadu autorizace | 10 | |
| Hodnota deklarace – maximální počet znaků | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limity vlastních konektorů

Tady jsou limity pro vlastní konektory, které můžete vytvořit z webových rozhraní API.

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| Počet vlastních konektorů | 1 000 na předplatné Azure | 1 000 na předplatné Azure ||
| Počet požadavků za minutu pro vlastní konektor | 500 požadavků za minutu na připojení | 2 000 požadavků za minutu na *vlastní konektor* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Spravované identity

| Name | Omezení |
|------|-------|
| Spravované identity na aplikaci logiky | Buď identita přiřazená systémem, nebo 1 uživatelem přiřazená identita |
| Počet aplikací logiky, které mají spravovanou identitu v předplatném Azure pro jednotlivé oblasti | 1 000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity účtu integrace

Každé předplatné Azure má tento účet pro integraci:

* Jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-pricing.md#integration-accounts) na oblast Azure. Tato úroveň je dostupná jenom pro veřejné oblasti v Azure, například Západní USA nebo jihovýchodní Asie, ale ne pro [Azure Čína 21Vianet](/azure/china/overview-operations) nebo [Azure Government](../azure-government/documentation-government-welcome.md).

* 1 000 Celkový počet integračních účtů, včetně integračních účtů v jakémkoli [prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) napříč [SKU pro vývojáře a úrovně Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Každý ISE, ať už [vývojář nebo Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), může používat jediný účet pro integraci bez dalších poplatků, i když se zahrnutý typ účtu liší podle ISE SKU. Můžete vytvořit další účty pro integraci pro ISE až do celkového limitu za [Další náklady](logic-apps-pricing.md#fixed-pricing):

  | SKU ISE | Limity účtu integrace |
  |---------|----------------------------|
  | **Premium** | 20 celkových účtů, včetně jednoho standardního účtu, bez dalších nákladů. Pomocí této SKU můžete mít jenom [standardní](../logic-apps/logic-apps-pricing.md#integration-accounts) účty. Nejsou povoleny žádné bezplatné nebo základní účty. |
  | **Vývojář** | 20 celkových účtů, včetně jednoho [bezplatného](../logic-apps/logic-apps-pricing.md#integration-accounts) účtu (omezeno na 1). U této SKU můžete mít buď kombinaci: <p>– Bezplatný účet a až 19 [standardních](../logic-apps/logic-apps-pricing.md#integration-accounts) účtů. <br>– Žádný bezplatný účet a až 20 standardních účtů. <p>Nejsou povolené žádné základní ani další bezplatné účty. <p><p>**Důležité**: použijte [SKU pro vývojáře](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) pro experimentování, vývoj a testování, ale ne pro produkční nebo výkonnostní testování. |
  |||

Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Omezení počtu artefaktů na účet pro integraci

Tady jsou limity pro počet artefaktů pro každou vrstvu integračního účtu. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o cenách a fakturační práci pro účty pro integraci najdete v [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Úroveň Free se používá jenom pro scénáře průzkumného prostředí, nikoli pro produkční scénáře. Tato úroveň omezuje propustnost a využití a nemá smlouvu o úrovni služeb (SLA).

| Artefakt | Free | Basic | Standard |
|----------|------|-------|----------|
| Obchodní smlouvy EDI | 10 | 1 | 1 000 |
| Obchodní partneři EDI | 25 | 2 | 1 000 |
| Maps | 25 | 500 | 1 000 |
| Schémata | 25 | 500 | 1 000 |
| Sestavení | 10 | 25 | 1 000 |
| Certifikáty | 25 | 2 | 1 000 |
| Konfigurace dávek | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Omezení kapacity artefaktů

| Artefakt | Omezení | Poznámky |
| -------- | ----- | ----- |
| Sestavení | 8 MB | Pokud chcete nahrávat soubory větší než 2 MB, použijte [účet úložiště Azure a kontejner objektů BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (soubor XSLT) | 8 MB | Chcete-li odeslat soubory větší než 2 MB, použijte [mapování Azure Logic Apps REST API](/rest/api/logic/maps/createorupdate). <p><p>**Poznámka**: objem dat nebo záznamů, které může mapa úspěšně zpracovat, vychází z omezení velikost zprávy a časový limit akcí v Azure Logic Apps. Pokud například použijete akci HTTP na základě [velikosti zprávy HTTP a omezení časového limitu](#http-limits), může mapa zpracovat data až do limitu velikosti zprávy HTTP, pokud se operace dokončí v rámci limitu časového limitu protokolu HTTP. |
| Schéma | 8 MB | Pokud chcete nahrávat soubory větší než 2 MB, použijte [účet úložiště Azure a kontejner objektů BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Omezení propustnosti

| Koncový bod modulu runtime | Free | Basic | Standard | Poznámky |
|------------------|------|-------|----------|-------|
| Čtení hovorů za 5 minut | 3 000 | 30 000 | 60 000 | Toto omezení platí pro volání, která získávají nezpracované vstupy a výstupy z historie spuštění aplikace logiky. V případě potřeby můžete úlohy distribuovat napříč více než jedním účtem. |
| Vyvolat volání za 5 minut | 3 000 | 30 000 | 45 000 | V případě potřeby můžete úlohy distribuovat napříč více než jedním účtem. |
| Sledování hovorů za 5 minut | 3 000 | 30 000 | 45 000 | V případě potřeby můžete úlohy distribuovat napříč více než jedním účtem. |
| Blokování souběžných volání | ~ 1 000 | ~ 1 000 | ~ 1 000 | Stejné pro všechny skladové položky. Můžete snížit počet souběžných požadavků nebo snížit dobu trvání podle potřeby. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Velikost zprávy protokolu B2B (AS2, X12, EDIFACT)

Tady jsou omezení velikosti zpráv, která se vztahují na protokoly B2B:

| Name | Limit více tenantů | Omezení prostředí integrační služby | Poznámky |
|------|--------------------|---------------------------------------|-------|
| AS2 | V2 – 100 MB<br>V1 – 25 MB | V2 – 200 MB <br>V1 – 25 MB | Platí pro dekódování a kódování. |
| X12 | 50 MB | 50 MB | Platí pro dekódování a kódování. |
| EDIFACT | 50 MB | 50 MB | Platí pro dekódování a kódování. |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Zakázání nebo odstranění Logic Apps

Když aplikaci logiky zakážete, nevytvoří se žádná nová spuštění. Všechny probíhající a nedokončené běhy pokračují, dokud se nedokončí, což může chvíli trvat.

Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Konfigurace brány firewall: IP adresy a značky služeb

Když aplikace logiky potřebuje komunikovat přes bránu firewall, která omezuje provoz na konkrétní IP adresy, musí tato brána firewall povolit přístup *pro* [příchozí](#inbound) i [odchozí](#outbound) IP adresy, které používá služba Logic Apps nebo modul runtime v oblasti Azure, ve které vaše aplikace logiky existuje. *Všechny* Logic Apps ve stejné oblasti používají stejné rozsahy IP adres.

Například pro podporu volání, která Logic Apps v Západní USA oblasti odesílají nebo přijímají prostřednictvím integrovaných triggerů a akcí, jako je například [Trigger nebo akce http](../connectors/connectors-native-http.md), musí brána firewall povolit přístup pro *všechny* příchozí IP adresy služby Logic Apps *a* odchozí IP adresy, které existují v oblasti západní USA.

Pokud vaše aplikace logiky používá i [spravované konektory](../connectors/apis-list.md#managed-api-connectors), jako je například konektor Office 365 Outlook nebo konektor SQL, nebo používá [vlastní konektory](/connectors/custom-connectors/), musí brána firewall také umožňovat přístup pro *všechny* [odchozí IP adresy spravovaného konektoru](#outbound) v oblasti Azure vaší aplikace logiky. Pokud navíc používáte vlastní konektory, které přistupují k místním prostředkům prostřednictvím [prostředku místní brány dat v Azure](logic-apps-gateway-connection.md), je třeba nastavit instalaci brány tak, aby povolovala přístup pro příslušné *[odchozí IP adresy](#outbound)spravovaných konektorů*.

Další informace o nastavení komunikace v bráně najdete v těchto tématech:

* [Úprava nastavení komunikace pro místní bránu dat](/data-integration/gateway/service-gateway-communication)
* [Konfigurace nastavení proxy serveru pro místní bránu dat](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Otázky konfigurace IP adresy brány firewall

Než nastavíte bránu firewall s IP adresami, přečtěte si tyto požadavky:

* Pokud používáte [Power Automatizujte](/power-automate/getting-started), můžete některé akce, jako je **http** a **http + openapi**, přejít přímo prostřednictvím služby Azure Logic Apps a pocházet z IP adres, které jsou tady uvedené. Další informace o IP adresách, které používá Power automat, najdete v tématu věnovaném [omezením a konfiguraci pro automatizaci napájení](/flow/limits-and-config#ip-address-configuration).

* Pro [Azure Čína 21Vianet](/azure/china/)jsou pevné nebo rezervované IP adresy nedostupné pro [vlastní konektory](../logic-apps/custom-connector-overview.md) a pro [spravované konektory](../connectors/apis-list.md#managed-api-connectors), jako je Azure Storage, SQL Server, Office 365 Outlook atd.

* Pokud vaše aplikace logiky běží v [prostředí ISE (Integration Service Environment)](connect-virtual-network-vnet-isolated-environment-overview.md), ujistěte se, že jste [tyto porty otevřeli](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Abychom vám pomohli zjednodušit všechna pravidla zabezpečení, která chcete vytvořit, můžete místo toho namísto zadání předpon IP adres pro každou oblast použít [značky služby](../virtual-network/service-tags-overview.md) . Tyto značky fungují v oblastech, kde je služba Logic Apps k dispozici:

  * **LogicAppsManagement**: představuje předpony příchozích IP adres pro službu Logic Apps.

  * **LogicApps**: představuje předpony odchozí IP adresy pro službu Logic Apps.

  * **AzureConnectors**: představuje předpony IP adres pro spravované konektory, které umožňují příchozí zpětná volání webhooků službě Logic Apps a odchozí volání jejich příslušných služeb, jako je například Azure Storage nebo Azure Event Hubs.

* Pokud vaše aplikace logiky mají problémy s přístupem k účtům Azure Storage, které používají [brány firewall a pravidla brány firewall](../storage/common/storage-network-security.md), máte k dispozici [různé možnosti pro povolení přístupu](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Například Logic Apps nemůžou přímo přistupovat k účtům úložiště, které používají pravidla brány firewall a existují ve stejné oblasti. Pokud ale povolíte [odchozí IP adresy pro spravované konektory ve vaší oblasti](../logic-apps/logic-apps-limits-and-config.md#outbound), můžou vaše aplikace logiky přistupovat k účtům úložiště, které jsou v jiné oblasti, s výjimkou případů, kdy používáte konektory Azure Table Storage nebo Azure Queue Storage. Pro přístup k Table Storage nebo Queue Storage můžete místo toho použít Trigger HTTP a akce. Další možnosti najdete v tématu [přístup k účtům úložiště za branami firewall](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Příchozí IP adresy

V této části jsou uvedeny příchozí IP adresy pouze pro službu Azure Logic Apps. Pokud máte Azure Government, přečtěte si téma [IP adresy Azure Government příchozích připojení](#azure-government-inbound).

> [!TIP]
> Chcete-li při vytváření pravidel zabezpečení zjednodušit složitost, můžete místo zadání předpony IP adres příchozích Logic Apps pro každou oblast použít [značku služby](../virtual-network/service-tags-overview.md) **LogicAppsManagement**. Volitelně můžete také použít značku služby **AzureConnectors** pro spravované konektory, které provádějí příchozí zpětná volání webhooků do služby Logic Apps, a nemusíte pro každou oblast zadávat předpony IP adres spravovaného konektoru. Tyto značky fungují v oblastech, kde je služba Logic Apps k dispozici.
>
> Následující konektory umožňují příchozí zpětná volání Webhooku službě Logic Apps:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Signing demo, Adobe Sign Preview, Adobe Signing Stage, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign demo, Dynamics 365 for fin & Operations, LiveChat, Office 365 Outlook, Outlook.com, analyzátor, SAP *, SHIFTS for Microsoft teams, projekty pro týmovou práci, Typeform
>
> \***SAP**: volající závisí na tom, jestli je prostředí pro nasazení víc tenantů Azure, nebo ISE. V prostředí s více klienty provádí místní brána dat zpětné volání služby Logic Apps. V ISE konektor SAP provede zpětné volání do služby Logic Apps.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Víceklientské IP adresy Azure – odchozí

| Oblast s více klienty | IP adresa |
|---------------------|----|
| Austrálie – východ | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Austrálie – jihovýchod | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazílie – jih | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Brazílie – jihovýchod | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Střední Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada – východ | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indie – střed | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA – střed | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Východní Asie | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA – východ 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Francie – střed | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Francie – jih | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Německo – sever | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Německo – středozápad | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Japonsko – východ | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japonsko – západ | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Jižní Korea – střed | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Jižní Korea – jih | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA – středosever | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Severní Evropa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Norsko – východ | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Jižní Afrika – sever | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Jižní Afrika – západ | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| Středojižní USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie – jih | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Southeast Asia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Švýcarsko – sever | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Švýcarsko – západ | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Spojené arabské emiráty – střed | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Spojené arabské emiráty sever | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Spojené království – jih | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Spojené království – západ | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA – středozápad | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| West Europe | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Západní Indie | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA – západ | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Západní USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
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

V této části jsou uvedeny odchozí IP adresy pro službu Azure Logic Apps a spravované konektory. Pokud máte Azure Government, přečtěte si téma [Azure Government-odchozí IP adresy](#azure-government-outbound).

> [!TIP]
> Chcete-li při vytváření pravidel zabezpečení zjednodušit složitost, můžete místo zadání předpony IP adresy odchozího Logic Apps pro každou oblast použít [značku služby](../virtual-network/service-tags-overview.md) **LogicApps**. Volitelně můžete také použít značku služby **AzureConnectors** pro spravované konektory, které provedou odchozí volání jejich příslušných služeb, jako je Azure Storage nebo Azure Event Hubs, a nemusíte pro každou oblast zadávat předpony IP adres pro odchozí spravované konektory. Tyto značky fungují v oblastech, kde je služba Logic Apps k dispozici.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Víceklientské Azure – odchozí IP adresy

| Oblast s více klienty | Logic Apps IP | IP adresa spravovaného konektoru |
|---------------------|---------------|-----------------------|
| Austrálie – východ | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Austrálie – jihovýchod | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Brazílie – jih | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Brazílie – jihovýchod | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26 |
| Střední Kanada | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Kanada – východ | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Indie – střed | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| USA – střed | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Východní Asie | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| USA – východ 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Francie – střed | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Francie – jih | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Německo – sever | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Německo – středozápad | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Japonsko – východ | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Japonsko – západ | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Jižní Korea – střed | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Jižní Korea – jih | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| USA – středosever | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Severní Evropa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Norsko – východ | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Jižní Afrika – sever | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Jižní Afrika – západ | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| Středojižní USA | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Indie – jih | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Southeast Asia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Švýcarsko – sever | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Švýcarsko – západ | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| Spojené arabské emiráty – střed | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Spojené arabské emiráty sever | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Spojené království – jih | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Spojené království – západ | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| USA – středozápad | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| West Europe | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Západní Indie | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| USA – západ | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| Západní USA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government – odchozí IP adresy

| Oblast | Logic Apps IP | IP adresa spravovaného konektoru |
|--------|---------------|-----------------------|
| US DoD – střed | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| USA (Gov) – Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| USA (Gov) – Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| USA (Gov) – Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) .
* Další informace o [běžných příkladech a scénářích](../logic-apps/logic-apps-examples-and-scenarios.md)
