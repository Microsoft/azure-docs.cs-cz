---
title: Registr schémat Azure v Event Hubs (Preview)
description: Tento článek poskytuje přehled podpory registru schématu pomocí Azure Event Hubs (Preview).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330491"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Registr schémat Azure v Event Hubs (Preview)
V mnoha scénářích pro streamování událostí a zasílání zpráv obsahuje datová část události nebo zprávy strukturovaná data, která jsou buď serializovaná, nebo deserializovaná pomocí formátu založeného na schématu, jako je Apache Avro. Odesílatelé i přijímače můžou chtít ověřit integritu dat pomocí schématu dokumentu jako ve schématu JSON. V případě formátů založených na schématu je k dispozici pro příjemce zprávy je předpokladem, že příjemce může data deserializovat. 

**Registry schématu Azure** je funkce Event Hubs, která poskytuje centrální úložiště pro dokumenty schématu pro aplikace zaměřené na události a pro zasílání zpráv. Poskytuje flexibilitu pro vaše aplikace producenta a příjemce pro výměnu dat bez nutnosti spravovat a sdílet schéma mezi nimi a také vyvíjet různými sazbami. Registr schématu také poskytuje jednoduché rozhraní zásad správného řízení pro opakovaně použitelná schémata a definuje vztah mezi schématy prostřednictvím seskupovací konstrukce (skupiny schémat).

> [!NOTE]
> - Funkce **registru schématu** je momentálně ve **verzi Preview** a nedoporučuje se pro produkční úlohy.
> - Tato funkce je k dispozici pouze ve **standardních** a **vyhrazených** vrstvách, nikoli na úrovni **Basic** .

Rozhraní serializace založená na schématu, jako je Apache Avro, přenesení metadata serializace do sdílených schémat, mohou také pomáhat s výrazným omezením režie za zprávy typu a názvy polí, které jsou zahrnuty v každé sadě dat, protože se jedná o případ s tagovanými formáty, jako je například JSON. Díky schématům uloženým společně s událostmi a uvnitř infrastruktury pro události je zajištěno, že metadata požadovaná pro serializaci/deserializaci jsou vždy ve dosahu a schémata nelze ukládat do nesprávného umístění. 

## <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs
Obor názvů Event Hubs nyní může hostovat skupiny schémat spolu s centrem událostí (nebo tématy Kafka). Hostuje registr schématu a může mít několik skupin schémat. Kromě toho, že se hostuje v Azure Event Hubs, se registr schémat dá používat univerzálně se všemi službami Azure Messaging Services a dalšími zprostředkovateli zpráv a událostí. Každá z těchto skupin schémat je samostatně zabezpečitelné úložiště pro sadu schémat. Skupiny je možné zarovnávat podle konkrétní aplikace nebo organizační jednotky. 

## <a name="schema-groups"></a>Skupiny schémat
Skupina schémat je logická skupina podobných schémat založená na vašich obchodních kritériích. Skupina schémat může obsahovat více verzí schématu. Nastavení vynucení kompatibility ve skupině schémat vám může pomáhat zajistit, aby novější verze schématu byly zpětně kompatibilní.

Hranice zabezpečení, která je pořízená mechanismem seskupení, pomáhá zajistit, aby obchodní tajemství nechtěně neunikla prostřednictvím metadat v situacích, kdy je obor názvů sdílen mezi více partnery. Také umožňuje vlastníkům aplikace spravovat schémata nezávisle na jiných aplikacích, které sdílejí stejný obor názvů.


## <a name="schemas"></a>Schémata
Schémata definují smlouvu mezi producenty a příjemci. Schéma definované v registru schématu Event Hubs pomáhá spravovat kontrakt mimo data události, čímž se odebírá režie na datovou část. Schéma má název, typ (příklad: záznam, pole a tak dále), režim kompatibility (žádné, dopředu, zpět, úplný) a typ serializace (nyní Avro). Můžete vytvořit několik verzí schématu a načíst a použít konkrétní verzi schématu. 

## <a name="client-sdks"></a>Klientské sady SDK
Můžete použít jednu z následujících knihoven, které obsahují serializátor Avro, který můžete použít k serializaci a deserializaci datových částí, které obsahují identifikátory schémat registru schématu a data zakódovaná Avro.

- [.NET – Microsoft. Azure. data. SchemaRegistry. ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java – Azure – Data-schemaregistry – Avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python – Azure – schemaregistry – avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScriptu @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) – spouštějte Kafka a deserializace integrovaných serializátorů Apache Avro v registru schématu Azure. Serializátor klienta Apache Kafka klienta Java pro Registry schématu Azure je možné použít v jakémkoli scénáři Apache Kafka a v případě nasazení založeného na Apache Kafka® nebo cloudové služby. 

Následující obrázek znázorňuje tok informací v registru schématu pomocí Event Hubs: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Diagram toku":::

## <a name="standard-vs-dedicated-limits"></a>Standardní vs. vyhrazená omezení
Pro omezení (například počet skupin schémat v oboru názvů), které jsou stejné a rozdílné pro standardní a vyhrazené úrovně Event Hubs, přečtěte si téma [omezení registru schématu](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations) .

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure
Při přistupování k registru schématu prostřednictvím kódu programu je nutné zaregistrovat aplikaci v Azure Active Directory (Azure AD) a přidat objekt zabezpečení aplikace do jedné z rolí řízení přístupu na základě role Azure (Azure RBAC):

| Role | Popis | 
| ---- | ----------- | 
| Vlastník | Čtení, zápis a odstraňování skupin registru schémat a schémat. |
| Přispěvatel | Čtení, zápis a odstraňování skupin registru schémat a schémat. |
| [Nástroj pro čtení registru schématu (Preview)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Čtení a výpis skupin registru a schémat registru. |
| [Přispěvatel registru schématu (Preview)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Čtení, zápis a odstraňování skupin registru schémat a schémat. |

Pokyny k vytvoření registrace aplikace pomocí Azure Portal najdete v tématu [Registrace aplikace pomocí Azure AD](../active-directory/develop/quickstart-register-app.md). Poznamenejte si ID klienta (ID aplikace), ID tenanta a tajný klíč, který chcete použít v kódu. 

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak vytvořit registr schématu pomocí Azure Portal, najdete v tématu [Vytvoření registru schématu Event Hubs pomocí Azure Portal](create-schema-registry.md).
- Podívejte se na následující ukázky **schématu Avro na klientské knihovny registru** .
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro Integration pro Azure Schema Registry](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
