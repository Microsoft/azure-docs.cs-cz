---
title: Konektory pro Azure Logic Apps
description: Přehled konektorů pro vytváření automatizovaných pracovních postupů pomocí Azure Logic Apps. Přečtěte si, jak různé typy konektorů, triggerů a akcí fungují.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771348"
---
# <a name="connectors-for-azure-logic-apps"></a>Konektory pro Azure Logic Apps

*Konektory* v Azure Logic Apps vám pomůžou rychle přistupovat k událostem, datům a akcím z jiných aplikací, služeb, systémů, protokolů a platforem. Tyto úlohy často můžete provádět bez dalšího kódu. Pomocí konektorů můžete také vytvářet Logic Apps pracovní postupy, které používají tyto informace v cloudových, místních nebo hybridních prostředích.

K dispozici jsou stovky konektorů pro Logic Apps. V důsledku toho se tato dokumentace zaměřuje na některé z nejoblíbenějších a běžně používaných konektorů pro Logic Apps. Úplné informace o konektorech napříč Logic Apps, Microsoft Power automatizuje a Microsoft Power Apps najdete v [dokumentaci ke konektorům](/connectors). 

Informace o cenách najdete v tématu [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md)a [Logic Apps podrobnosti o cenách](https://azure.microsoft.com/pricing/details/logic-apps/).

Pro integraci aplikace logiky se službou nebo rozhraním API, které nemá konektor, můžete buď zavolat službu přes protokol, například HTTP, nebo [vytvořit vlastní konektor](#custom-apis-and-connectors).

## <a name="what-are-connectors"></a>Co jsou konektory?

V Azure Logic Apps konektory poskytují *triggery* a *Akce* , které můžete použít k provádění úloh v pracovním postupu vaší aplikace logiky. Každá aktivační událost a akce mají vlastnosti, které lze konfigurovat. Některé triggery a akce vyžadují, abyste [vytvořili a nakonfigurovali připojení](#connection-configuration) , aby váš pracovní postup mohl přistupovat ke konkrétní službě nebo systému.

### <a name="triggers"></a>Aktivační události

*Trigger* je vždy první krok v jakémkoli pracovním postupu, který určuje událost, která spouští pracovní postup. K dispozici je několik typů triggerů:

- *Triggery cyklického dotazování* pravidelně kontrolují určitou službu nebo systém podle zadaného plánu a kontrolují nová data nebo konkrétní událost. Pokud jsou k dispozici nová data nebo dojde ke konkrétní události, tyto aktivační události vytvoří a spustí novou instanci pracovního postupu. Tato nová instance pak může použít data, která se předávají jako vstup.
- *Triggery nabízených oznámení* naslouchají novým datům nebo k události, ke kterým dojde, bez cyklického dotazování. Když jsou k dispozici nová data nebo dojde k události, tyto triggery vytvoří a spustí novou instanci vaší aplikace logiky. Tato nová instance pak může použít data, která se předávají jako vstup.

Můžete například chtít vytvořit pracovní postup, který bude při nahrávání souboru na server FTP nějaký. Můžete přidat Trigger konektoru FTP s názvem, **když se přidá nebo upraví soubor**, jako první krok v pracovním postupu. Pak můžete zadat plán, který bude pravidelně kontrolovat události odeslání.

Aktivační událost také předá všechny vstupy a další požadovaná data do pracovního postupu, kde později mohou akce odkazovat a používat tato data v rámci pracovního postupu. Například můžete chtít použít aktivační událost sady Office 365 Outlook s názvem **při přijetí nového e-mailu**. Tuto aktivační událost můžete nakonfigurovat tak, aby předávala obsah z každého nového e-mailu, jako je odesilatel, řádek předmětu, tělo, přílohy a tak dále. Pak můžete tyto informace zpracovat v aplikaci logiky pomocí akcí.

### <a name="actions"></a>Akce

*Akce* je operace, která následuje po triggeru a ve vašem pracovním postupu provede nějaký druh úlohy. V aplikaci logiky můžete použít více akcí. Můžete mít například Trigger SQL, který detekuje nová zákaznická data v databázi SQL. Pracovní postup může zahrnovat první akci SQL, která získá zákaznická data a za ní následuje jiná akce, která není nutně akcí SQL, která zpracovává data.

## <a name="connector-categories"></a>Kategorie konektoru

V Logic Apps jsou obvykle integrované nebo spravované verze konektoru triggery a akce. V obou verzích je k dispozici malý počet triggerů a akcí. Konkrétní verze závisí na tom, jestli vytváříte aplikaci logiky pro více tenantů nebo aplikaci logiky pro jednoho tenanta, která je aktuálně dostupná jenom ve [verzi Logic Apps Preview](../logic-apps/logic-apps-overview-preview.md).

[Integrované triggery a akce](built-in.md) se spouštějí nativně v prostředí Logic Apps runtime, nevyžadují vytváření připojení a provádějí tyto typy úloh:

- [Spusťte kód v pracovních postupech](built-in.md#run-code-from-workflows).
- [Uspořádejte a kontrolujte svá data](built-in.md#control-workflow).
- [Umožňuje spravovat data a manipulovat s nimi](built-in.md#manage-or-manipulate-data).

[Spravované konektory](managed.md) se nasazují, hostují a spravují Microsoftem. Tyto konektory poskytují aktivační události a akce pro cloudové služby, místní systémy nebo obojí. Tady jsou některé z nich:

- [Místní konektory](managed.md#on-premises-connectors) , které vám pomůžou s přístupem k datům a prostředkům v místních systémech.
- [Podnikové konektory](managed.md#enterprise-connectors), což jsou verze některých Logic Apps konektorů, které poskytují přístup k podnikovým systémům.
- [Konektory účtu pro integraci](managed.md#integration-account-connectors), které podporují komunikační scénáře B2B (Business-to-Business).
- [Konektory prostředí integrační služby (ISE)](managed.md#ise-connectors) , které jsou malou skupinou [spravovaných konektorů, jsou dostupné jenom pro ISEs](#ise-and-connectors).

## <a name="connection-configuration"></a>Konfigurace připojení

Většina konektorů vyžaduje, abyste nejdřív vytvořili *připojení* k cílové službě nebo systému, abyste mohli použít jeho triggery nebo akce v aplikaci logiky. Pokud chcete vytvořit připojení, musíte ověřit identitu s přihlašovacími údaji účtu a někdy taky další informace o připojení. Například před tím, než bude váš pracovní postup mít přístup k e-mailovým účtům Office 365 Outlook v aplikaci logiky, musíte k tomuto účtu autorizovat připojení a pracovat s ním.

Pro konektory, které používají Azure Active Directory (Azure AD) OAuth, jako je například Office 365, Salesforce nebo GitHub, se musíte přihlásit ke službě, ve které je váš přístupový token [zašifrovaný](../security/fundamentals/encryption-overview.md) a bezpečně uložený v tajných klíčích Azure. Jiné konektory, například FTP a SQL, vyžadují připojení, které má podrobnosti o konfiguraci, jako je adresa serveru, uživatelské jméno a heslo. Tyto podrobnosti konfigurace připojení jsou taky [šifrované a bezpečně uložené v Azure](../security/fundamentals/encryption-overview.md).

Zavedená připojení mají přístup k cílové službě nebo systému, pokud to služba nebo systém umožňuje. Pro služby, které používají připojení Azure AD OAuth, jako je například Office 365 a Dynamics, Logic Apps aktualizuje přístupové tokeny po neomezenou dobu. Další služby mohou mít omezení doby, po kterou může služba Logic Apps používat token bez aktualizace. Obecně platí, že některé akce, jako je třeba Změna hesla, mají za následek zrušení platnosti všech přístupových tokenů.

I když v rámci pracovního postupu vytvoříte připojení, připojení jsou samostatné prostředky Azure s vlastními definicemi prostředků. Pokud si chcete prohlédnout tyto definice prostředků připojení, [Stáhněte si aplikaci logiky z Azure do sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md). Tato metoda představuje nejjednodušší způsob, jak vytvořit platnou, parametrizovanou šablonu aplikace logiky, která je většinou připravena pro nasazení.

> [!TIP]
> Pokud vaše organizace neumožňuje přístup ke konkrétním prostředkům prostřednictvím Logic Appsch konektorů, můžete [zablokovat možnost vytvářet taková připojení](../logic-apps/block-connections-connectors.md) pomocí [Azure Policy](../governance/policy/overview.md).

## <a name="recurrence-behavior"></a>Chování opakování

Opakující se vestavěné triggery, jako je například [Trigger opakování](../connectors/connectors-native-recurrence.md), se nativně spouštějí v Azure Logic Apps a liší se od opakovaných triggerů na základě připojení, jako je například aktivační událost konektoru sady Office 365 Outlook, kde je třeba nejprve vytvořit připojení.

Pokud v obou typech triggerů neurčí určité počáteční datum a čas, první opakování se spustí okamžitě při uložení nebo nasazení aplikace logiky, a to bez ohledu na nastavení opakování triggeru. Chcete-li se tomuto chování vyhnout, zadejte počáteční datum a čas, kdy chcete spustit první opakování.

### <a name="recurrence-for-built-in-triggers"></a>Opakování vestavěných triggerů

Opakující se integrované aktivační události dodržují plán, který jste nastavili, včetně jakéhokoli zadaného časového pásma. Pokud však opakování neurčí další možnosti pokročilého plánování, například konkrétní časy spuštění budoucích opakování, budou tato opakování vycházet z posledního spuštění aktivační události. V důsledku toho by časy zahájení těchto opakování mohly vzniknout kvůli faktorům, jako je latence během volání úložiště. Nápovědu k odstraňování potíží najdete v části [s problémy opakování](#recurrence-issues) .

### <a name="recurrence-for-connection-based-triggers"></a>Opakování pro aktivační události na základě připojení

V případě opakovaných triggerů na základě připojení, jako je například Office 365 Outlook, není plán jediným ovladačem, který řídí provádění. Časové pásmo určuje pouze počáteční čas spuštění. Další spuštění závisí na plánu opakování, posledním spuštění triggeru a dalších faktorech, které by mohly způsobit, že doba běhu vznikne nebo neočekávané chování. Tady jsou některé z nich:

- Určuje, jestli aktivační událost přistupuje k serveru, který má víc dat, která se Trigger okamžitě pokusí načíst.
- Dojde k chybám nebo opakovaným pokusům o aktivaci triggeru.
- Latence během volání úložiště
- Neúdržba zadaného plánu při zahájení a ukončení letního času (DST).
- Další faktory, které mohou ovlivnit čas příštího spuštění.

Nápovědu k odstraňování potíží najdete v části [s problémy opakování](#recurrence-issues) . 

### <a name="recurrence-issues"></a>Problémy opakování

Pokud chcete zajistit, aby se váš pracovní postup spouštěl v zadaném čase zahájení a neztratil opakování, zejména pokud je frekvence ve dnech nebo delší, vyzkoušejte následující řešení.

Když se letní čas projeví, proveďte ruční úpravu opakování, aby váš pracovní postup běžel i v očekávaném čase. V opačném případě se čas zahájení posune jednu hodinu dopředu při zahájení LETNÍho času a hodinu zpětně od konce LETNÍho času. Další informace a příklady naleznete v tématu [opakování pro letní čas a běžný čas](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

Pokud používáte Trigger **opakování** , zadejte časové pásmo, počáteční datum a čas. Navíc můžete nakonfigurovat konkrétní časy spouštění následných opakování ve vlastnostech v **těchto hodinách** a **v těchto minutách**, které jsou k dispozici pouze pro frekvence **dne** a **týdne** . Některá časová období ale můžou pořád způsobit problémy, když se posunou čas. 

Zvažte použití [ **posuvné** aktivační události okna](../connectors/connectors-native-sliding-window.md) místo triggeru **opakování** , aby nedocházelo k zmeškaným opakováním.

## <a name="custom-apis-and-connectors"></a>Vlastní rozhraní API a konektory

Chcete-li volat rozhraní API, která spouští vlastní kód nebo nejsou k dispozici jako konektory, můžete Logic Apps platformu rozšíření [vytvořit vlastní API Apps](../logic-apps/logic-apps-create-api-app.md). 

Můžete také [vytvořit vlastní konektory](../logic-apps/custom-connector-overview.md) pro jakákoli rozhraní API založená na REST nebo protokolu SOAP, která zpřístupňují tato rozhraní API pro libovolnou aplikaci logiky ve vašem předplatném Azure. 

Pokud chcete, aby byly vlastní API Apps nebo konektory veřejné pro použití v Azure, můžete [Odeslat konektory pro certifikaci Microsoftu](/connectors/custom-connectors/submit-certification).

## <a name="ise-and-connectors"></a>ISE a konektory

Pro pracovní postupy, které potřebují přímý přístup k prostředkům ve službě Azure Virtual Network, můžete vytvořit vyhrazené [prostředí Integration Service (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , kde můžete vytvářet, nasazovat a spouštět pracovní postupy na vyhrazených prostředcích. Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

Vlastní konektory vytvořené v rámci ISE nefungují s místní bránou dat. Tyto konektory ale můžou přistupovat přímo k místním zdrojům dat, které jsou připojené k virtuální síti Azure hostující rozhraní ISE. Proto Logic Apps v ISE pravděpodobně nepotřebují bránu dat při komunikaci s těmito prostředky. Pokud máte vlastní konektory, které jste vytvořili mimo ISE, které vyžadují místní bránu dat, můžou tyto konektory používat Logic Apps v ISE.

Když v Návrháři Logic Apps procházíte konektory, které chcete použít pro Logic Apps v ISE, zobrazí se na integrovaných triggerech a akcích **základní** popisek a na některých konektorech se zobrazí popisek **ISE** .

:::row:::
    :::column:::
        ![Příklad základního konektoru](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        Vestavěné triggery a akce s tímto popiskem se spouštějí ve stejném ISE jako vaše aplikace logiky.
    :::column-end:::
    :::column:::
        ![Příklad konektoru ISE](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        Spravované konektory s tímto popiskem běží ve stejném ISE jako vaše aplikace logiky. Pokud máte místní systém, který je připojený ke službě Azure Virtual Network, ISE umožňuje aplikacím logiky přímý přístup k tomuto systému bez [místní brány dat](../logic-apps/logic-apps-gateway-connection.md). Místo toho můžete použít konektor **ISE** tohoto systému, pokud je k dispozici, akci HTTP nebo [vlastní konektor](connectors-overview.md#custom-apis-and-connectors). Pro místní systémy, které nemají konektory **ISE** , použijte místní bránu dat. Pokud chcete zkontrolovat dostupné konektory ISE, přečtěte si téma [konektory ISE](#ise-and-connectors).
    :::column-end:::
    :::column:::
        ![Příklad konektoru s více klienty](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Bez popisku     \
        \
        Všechny ostatní konektory bez označení **Core** nebo **ISE** , které můžete dál používat, běží v globální Logic Apps službě pro více tenantů.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Známé problémy

Níže jsou uvedené známé problémy pro Logic Apps konektory.

#### <a name="error-badgateway-client-request-id-guid"></a>Chyba: BadGateway. ID žádosti klienta: {GUID}

Tato chyba je výsledkem aktualizace značek aplikace logiky, kde jedno nebo více připojení nepodporují ověřování OAuth služby Azure Active Directory (Azure AD), jako je například SFTP AD SQL, přerušují tato připojení. Aby se zabránilo tomuto chování, neaktualizujte tyto značky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření vlastních rozhraní API, která můžete volat z Logic Apps](/logic-apps/logic-apps-create-api-app)
