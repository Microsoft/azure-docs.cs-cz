---
title: Zabezpečení pro řešení Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Seznamte se s osvědčenými postupy zabezpečení u digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d129890cc63e5a0e4cbc6139e1de2df7a31f6f77
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094569"
---
# <a name="secure-azure-digital-twins"></a>Zabezpečení digitálních vláken Azure

Z důvodu zabezpečení umožňuje digitální vlákna Azure přesné řízení přístupu ke konkrétním datům, prostředkům a akcím v nasazení. Provede to prostřednictvím podrobné strategie správy rolí a oprávnění označovaného jako **řízení přístupu na základě role Azure (Azure RBAC)**. Obecné principy Azure RBAC si můžete přečíst [tady](../role-based-access-control/overview.md).

Digitální vlákna Azure také podporuje šifrování dat v klidovém umístění.

## <a name="roles-and-permissions-with-azure-rbac"></a>Role a oprávnění s Azure RBAC

Služba Azure RBAC se poskytuje pro digitální vlákna Azure prostřednictvím integrace s [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Službu Azure RBAC můžete použít k udělení oprávnění *objektu zabezpečení*, který může být uživatel, skupina nebo instanční objekt aplikace. Objekt zabezpečení je ověřený službou Azure AD a při návratu obdrží token OAuth 2,0. Tento token se dá použít k autorizaci žádosti o přístup k instanci digitálních vláken Azure.

### <a name="authentication-and-authorization"></a>Ověřování a autorizace

S Azure AD je přístup k procesu se dvěma kroky. Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí získat přístup k digitálním podmnožinám Azure, musí být požadavek *ověřený* a *autorizovaný*. 

1. Nejprve je *ověřená* identita objektu zabezpečení a je vrácen token OAuth 2,0.
2. V dalším kroku se token předává jako součást požadavku službě Azure Digital zdvojené služby k *autorizaci* přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby každá žádost aplikace při spuštění obsahovala přístupový token OAuth 2,0. Pokud je aplikace spuštěná v rámci entity Azure, jako je například aplikace [Azure Functions](../azure-functions/functions-overview.md) , může pro přístup k prostředkům použít **spravovanou identitu** . Další informace o spravovaných identitách najdete v další části.

Autorizační krok vyžaduje, aby byla role Azure přiřazená k objektu zabezpečení. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Funkce digitálních vláken Azure poskytuje role Azure, které zahrnují sady oprávnění pro prostředky digitálních vláken Azure. Tyto role jsou popsány dále v tomto článku.

Další informace o rolích a přiřazení rolí podporovaných v Azure najdete v tématu [*pochopení různých rolí*](../role-based-access-control/rbac-and-directory-admin-roles.md) v dokumentaci k Azure RBAC.

#### <a name="authentication-with-managed-identities"></a>Ověřování pomocí spravovaných identit

[Spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/overview.md) je funkce mezi Azure, která umožňuje vytvořit zabezpečenou identitu přidruženou k nasazení, kde se spouští kód aplikace. Tuto identitu pak můžete přidružit k rolím řízení přístupu a udělit tak vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje.

Se spravovanými identitami spravuje platforma Azure tuto identitu modulu runtime. Nemusíte ukládat a chránit přístupové klíče v kódu nebo konfiguraci aplikace, a to buď pro samotnou identitu, nebo pro prostředky, ke kterým potřebujete přístup. Klientská aplikace pro digitální vlákna Azure běžící v aplikaci Azure App Service nepotřebuje zpracovávat pravidla a klíče SAS ani žádné jiné přístupové tokeny. Klientská aplikace potřebuje jenom adresu koncového bodu oboru názvů digitálních vláken Azure. Když se aplikace připojí, digitální vlákna Azure váže kontext spravované entity k klientovi. Po přidružení ke spravované identitě může klient Azure s digitálními podmnožinami provádět všechny autorizované operace. Autorizace se pak udělí přidružením spravované entity k roli Azure Digital-propojování Azure (popsané níže).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorizace: role Azure pro digitální vlákna Azure

Azure poskytuje **dvě předdefinované role Azure** pro autorizaci přístupu k [rozhraním API roviny dat](how-to-use-apis-sdks.md#overview-data-plane-apis)digitálních vláken Azure. Na role můžete odkazovat buď podle názvu, nebo podle ID:

| Předdefinovaná role | Popis | ID | 
| --- | --- | --- |
| Vlastník dat digitálních vláken Azure | Poskytuje úplný přístup k prostředkům digitálních vláken Azure. | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Čtečka dat digitálních vláken Azure | Poskytuje přístup jen pro čtení k prostředkům digitálních vláken Azure. | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Role můžete přiřadit dvěma způsoby:
* prostřednictvím podokna řízení přístupu (IAM) pro digitální vlákna Azure v Azure Portal (viz [*přiřazení rolí Azure pomocí Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* Přidání nebo odebrání role přes příkazy rozhraní příkazového řádku

Podrobnější pokyny k tomu, jak to provést, najdete v kurzu o digitálních Zdvojeních Azure [*: připojení kompletního řešení*](tutorial-end-to-end.md).

Další informace o tom, jak jsou předdefinované role definované, najdete v tématu [*vysvětlení definic rolí*](../role-based-access-control/role-definitions.md) v dokumentaci k Azure RBAC. Informace o vytváření vlastních rolí Azure najdete v tématu [*vlastní role Azure*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatizace rolí

Při odkazování na role v automatizovaných scénářích doporučujeme, abyste na ně odkazovali pomocí jejich **ID** , nikoli podle jejich názvů. Názvy se můžou mezi verzemi měnit, ale identifikátory nebudou, takže jsou v automatizaci více stabilními odkazy.

> [!TIP]
> Pokud jste assiging role pomocí rutiny, jako je například `New-AzRoleAssignment` ([referenční](/powershell/module/az.resources/new-azroleassignment)dokumentace), můžete `-RoleDefinitionId` místo `-RoleDefinitionName` názvu role předat ID, a to pomocí parametru.

### <a name="permission-scopes"></a>Obory oprávnění

Než do objektu zabezpečení přiřadíte roli Azure, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že je nejvhodnější udělit jenom nejužšího možného rozsahu.

Následující seznam popisuje úrovně, na kterých můžete přistoupit k prostředkům v oblasti digitálních vláken Azure.
* Modely: akce pro tento prostředek určují kontrolu nad [modely](concepts-models.md) nahranými v digitálních činnostech Azure.
* Graf digitálního vyzdvojení dotazů: akce pro tento prostředek určují schopnost spouštět [operace dotazů](concepts-query-language.md) u digitálních vláken v grafu digitálních vláken Azure.
* Digitální vlákna: akce pro tento prostředek poskytují kontrolu nad operacemi CRUD u [digitálních vláken](concepts-twins-graph.md) ve dvojitých grafech.
* Digitální zdvojený vztah: akce pro tento prostředek definují kontrolu nad operacemi CRUD u [vztahů](concepts-twins-graph.md) mezi digitálními prvky v grafu s dvojitou závislostí.
* Trasa události: akce pro tento prostředek určují oprávnění k [Směrování událostí](concepts-route-events.md) z digitálních vláken Azure do služby koncového bodu, jako [je centrum událostí](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)nebo [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Řešení potíží s oprávněními

Pokud se uživatel pokusí provést akci, kterou role nepovoluje, může se zobrazit chyba z čtení žádosti o službu `403 (Forbidden)` . Další informace a postup pro řešení potíží najdete v tématu [*řešení potíží: žádost o digitální vlákna Azure se nezdařila se stavem: 403 (zakázáno)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Spravovaná identita pro přístup k jiným prostředkům (Preview)

Nastavení **spravované identity** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) pro instanci digitálních vláken Azure umožňuje instanci snadno přistupovat k dalším prostředkům chráněným službou Azure AD, jako je například [Azure Key Vault](../key-vault/general/overview.md). Identita je spravovaná platformou Azure a nevyžaduje zřízení ani střídání tajných kódů. Další informace o spravovaných identitách v Azure AD najdete v tématu  [*spravované identity pro prostředky Azure*](../active-directory/managed-identities-azure-resources/overview.md). 

Azure podporuje dva typy spravovaných identit: přiřazeno systémem a přiřazeno uživateli. V současné době digitální vlákna Azure podporuje jenom **identity přiřazené systémem**. 

Pro ověření pro [vlastní koncový bod definovaný](concepts-route-events.md#create-an-endpoint)systémem můžete použít spravovanou identitu vaší služby Azure Digital instance. Digitální vlákna Azure podporuje ověřování založené na identitě přiřazené systémem k koncovým bodům pro [centrum událostí](../event-hubs/event-hubs-about.md) a cíle [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   a do koncového bodu [kontejneru Azure Storage](../storage/blobs/storage-blobs-introduction.md)   pro [události nedoručených zpráv](concepts-route-events.md#dead-letter-events). [Event Grid](../event-grid/overview.md)   pro spravované identity se aktuálně nepodporují koncové body.

Pokyny k povolení identity spravované systémem pro digitální vlákna Azure a jejich použití ke směrování událostí najdete v tématu [*Postup: povolení spravované identity pro události směrování (Preview)*](how-to-enable-managed-identities.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Přístup k privátní síti pomocí privátního propojení Azure (Preview)

[Privátní propojení Azure](../private-link/private-link-overview.md) je služba, která umožňuje přístup k prostředkům Azure (jako je [azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md)a [Azure Cosmos DB](../cosmos-db/introduction.md)) a zákaznickým a partnerským službám hostovaným v Azure prostřednictvím privátního koncového bodu ve [službě Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md). 

Podobně můžete použít privátní koncové body pro instanci digitálního vlákna Azure, aby klienti ve vaší virtuální síti měli zabezpečený přístup k instanci prostřednictvím privátního propojení. 

Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě Azure. Síťový provoz mezi klientem v privátní síti a instancí digitálních vláken Azure prochází přes virtuální síť a privátní odkaz na páteřní síť Microsoftu, což eliminuje expozici veřejnému Internetu. Toto je vizuální znázornění tohoto systému:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Diagram znázorňující síť pro společnost PowerGrid, která je chráněnou virtuální sítí bez přístupu k Internetu a veřejným cloudem, a připojení prostřednictvím privátního propojení s instancí digitálních vláken Azure s názvem CityOfTwins.":::

Konfigurace privátního koncového bodu pro instanci digitálních vláken Azure vám umožní zabezpečit vaši instanci digitálních vláken Azure a eliminovat veřejnou expozici a zabránit tomu, aby se data exfiltrace z vaší virtuální sítě.

Pokyny, jak nastavit privátní odkaz pro digitální vlákna Azure, najdete v tématu [*How to: Enable privátní Access with Private Link (Preview)*](how-to-enable-private-link.md).

### <a name="design-considerations"></a>Na co dát pozor při navrhování 

Když pracujete s privátním odkazem na digitální vlákna Azure, tady je několik faktorů, které byste mohli zvážit:
* **Ceny**: informace o cenách najdete v tématu  [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link). 
* **Regionální dostupnost**: u digitálních vláken Azure je tato funkce dostupná ve všech oblastech Azure, kde jsou k dispozici digitální vlákna Azure. 
* **Maximální počet privátních koncových bodů na instanci digitálních vláken Azure**: 10

Informace o omezeních privátního propojení najdete v tématu [dokumentace k privátním linkám Azure: omezení](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Značky služeb

**Značka služby** představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služby najdete v tématu  [*značky virtuální sítě*](../virtual-network/service-tags-overview.md). 

Značky služeb můžete použít k definování řízení přístupu k síti u [skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md#security-rules)   nebo [Azure firewall](../firewall/service-tags.md)pomocí značek služby místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (v tomto případě **AzureDigitalTwins**) v příslušném *zdrojovém*   nebo *cílovém*   poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. 

Níže jsou uvedeny podrobnosti o značce služby **AzureDigitalTwins** .

| Značka | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Poznámka: Tato značka nebo IP adresy, na které se vztahuje tato značka, se dají použít k omezení přístupu k koncovým bodům nakonfigurovaným pro [trasy událostí](concepts-route-events.md). | Příchozí | No | Yes |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Použití značek služby pro přístup k koncovým bodům směrování událostí 

Tady je postup pro přístup k koncovým bodům [Směrování událostí](concepts-route-events.md) pomocí značek služeb s digitálními událostmi Azure.

1. Nejdřív si stáhněte tento odkaz na soubor JSON, který ukazuje rozsahy IP adres Azure a značky služeb: [*rozsahy IP adres Azure a značky služeb*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Vyhledejte rozsahy IP adres "AzureDigitalTwins" v souboru JSON.  

3. Informace o tom, jak nastavit filtry IP pro daný prostředek, najdete v dokumentaci k externímu prostředku připojenému ke koncovému bodu (například [Event Grid](../event-grid/overview.md), [centru událostí](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)nebo [Azure Storage](../storage/blobs/storage-blobs-overview.md) pro [události nedoručených zpráv](concepts-route-events.md#dead-letter-events)).

4. Nastavte filtry IP pro externí prostředky pomocí rozsahů IP adres z *kroku 2*.  

5. Aktualizujte rozsahy IP adres pravidelně podle potřeby. Rozsahy se můžou v průběhu času měnit, takže je dobré je pravidelně kontrolovat a v případě potřeby je aktualizovat. Frekvence těchto aktualizací se může lišit, ale je vhodné ji kontrolovat jednou týdně.

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění

Digitální vlákna Azure zajišťuje Šifrování neaktivních a přenosových dat, která se zapisují v našich datových centrech, a při přístupu k nim dešifruje. K tomuto šifrování dochází pomocí šifrovacího klíče spravovaného společností Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Digitální vlákna Azure v současné době nepodporuje **sdílení prostředků mezi zdroji (CORS)**. Výsledkem je, že pokud voláte REST API z aplikace v prohlížeči, rozhraní [API Management (APIM)](../api-management/api-management-key-concepts.md) nebo konektor [Power Apps](/powerapps/powerapps-overview) , může se zobrazit chyba zásad.

Tuto chybu můžete vyřešit provedením jedné z následujících akcí:
* Z zprávy vypruhute hlavičku CORS `Access-Control-Allow-Origin` . Tato hlavička uvádí, zda lze odpověď sdílet. 
* Případně můžete vytvořit proxy CORS a zajistit, aby se digitální vlákna Azure REST APIa prostřednictvím něj. 

## <a name="next-steps"></a>Další kroky

* Podívejte se na tyto koncepty v tématu [*Postup: nastavení instance a ověřování*](how-to-set-up-instance-portal.md).

* Informace o tom, jak s těmito koncepty pracovat z klientského kódu aplikace v tématu [*Postupy: psaní ověřovacího kódu aplikace*](how-to-authenticate-client.md).

* Přečtěte si další informace o [službě Azure RBAC](../role-based-access-control/overview.md).