---
title: Spravované identity pro Azure
description: Přečtěte si o používání spravovaných identit pro Azure pomocí Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: f6f3736bed4d3d59bce08d4df3ee0aa164a0a764
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415100"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Použití spravovaných identit pro Azure pomocí service fabricu

Běžnou výzvou při vytváření cloudových aplikací je, jak bezpečně spravovat přihlašovací údaje v kódu pro ověřování různých služeb bez jejich lokálního uložení na pracovní stanici pro vývojáře nebo ve správě zdrojového kódu. *Spravované identity pro Azure* tento problém vyřeší u všech vašich prostředků ve službě Azure Active Directory (Azure AD) tím, že jim poskytnete automaticky spravované identity v rámci Azure AD. Identitu služby můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření uložených ve vašem kódu.

*Spravované identity pro prostředky Azure* jsou zdarma s předplatnýmAzure AD pro Azure. Neúčtují se za ní žádné další poplatky.

> [!NOTE]
> *Spravované identity pro Azure* je nový název služby dříve známé jako identita spravované služby (MSI).

## <a name="concepts"></a>Koncepty

Spravované identity pro Azure jsou založeny na několika klíčových konceptech:

- **ID klienta** – jedinečný identifikátor generovaný službou Azure AD, který je vázán na instanční objekt aplikace a služby během počátečního zřizování (viz také [ID aplikace](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **ID objektu** - ID objektu objektu instancí pro spravovanou identitu, který se používá k udělení přístupu k prostředku Azure na základě rolí.

- **Instanční objekt** – objekt služby Azure Active Directory, který představuje projekci aplikace AAD v daném tenantovi (viz také [instanční objekt](../active-directory/develop/developer-glossary.md#service-principal-object).)

Existují dva typy spravovaných identit:

- **Spravovaná identita přiřazená systémem** je povolená přímo v instanci služby Azure.  Životní cyklus systémově přiřazené identity je jedinečný pro instanci služby Azure, na které je povolená.
- **Spravovaná identita přiřazená uživatelem** se vytváří jako samostatný prostředek Azure. Identitu lze přiřadit k jedné nebo více instancí služby Azure a spravuje se odděleně od životního cyklu těchto instancí.

Pokud chcete lépe pochopit rozdíl mezi typy spravovaných identit, [přečtěte si, jak fungují spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Podporované scénáře pro aplikace Service Fabric

Spravované identity pro Service Fabric jsou podporovány jenom v clusterech Service Fabric nasazených v Azure a jenom pro aplikace nasazené jako prostředky Azure. aplikace, která není nasazena jako prostředek Azure nelze přiřadit identitu. Koncepčně vzato se podpora spravovaných identit v clusteru Azure Service Fabric skládá ze dvou fází:

1. Přiřazení jedné nebo více spravovaných identit k prostředku aplikace. aplikaci může být přiřazena jediná systémově přiřazená identita a/nebo až 32 identit přiřazených uživateli.

2. V rámci definice aplikace namapujte jednu z identit přiřazených k aplikaci na jednotlivé služby, které tvoří aplikaci.

Systémem přiřazená identita aplikace je pro tuto aplikaci jedinečná. identita přiřazená uživatelem je samostatný prostředek, který může být přiřazen více aplikacím. V rámci aplikace lze jednu identitu (přiřazenou systémem nebo uživatelem) přiřadit k více službám aplikace, ale každé jednotlivé službě lze přiřadit pouze jednu identitu. Nakonec musí být službě přiřazena identita explicitně mít přístup k této funkci. Ve skutečnosti mapování identity aplikace na její základní služby umožňuje izolaci v aplikaci – služba může používat pouze identitu mapovanou na něj.  

V současné době jsou pro tuto funkci podporovány následující scénáře:

- Nasazení nové aplikace s jednou nebo více službami a jednou nebo více přiřazenými identitami

- Přiřazení jedné nebo více spravovaných identit k existující aplikaci (nasazené v Azure) za účelem přístupu k prostředkům Azure

Následující scénáře nejsou podporovány nebo nejsou doporučeny. Všimněte si, že tyto akce nemusí být blokovány, ale mohou vést k výpadkům ve vašich aplikacích:

- Odebrání nebo změna identit přiřazených aplikaci. Pokud je nutné provést změny, odešlete samostatná nasazení, abyste nejprve přidali nové přiřazení identity a potom odebrali dříve přiřazené. Odebrání identity z existující aplikace může mít nežádoucí účinky, včetně ponechání aplikace ve stavu, který nelze upgradovat. Je bezpečné odstranit aplikaci úplně, pokud je nutné odstranění identity; Tím odstraníte systémově přiřazenou identitu (pokud je tak toto definováno) přidružené k aplikaci a odebere se všechna přidružení k identitám přiřazeným uživateli přiřazeným k aplikaci.

- Podpora service fabric pro spravované identity není v tuto chvíli integrována do [zprostředkovatele AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md).

## <a name="next-steps"></a>Další kroky

- [Nasazení nového clusteru Azure Service Fabric s podporou spravovaných identit](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Využití spravované identity aplikace Service Fabric z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
- [Udělení přístupu aplikací Azure Service Fabric k dalším prostředkům Azure](./how-to-grant-access-other-resources.md)
- [Deklarování a používání tajných klíčů aplikace jako KeyVaultReferences](./service-fabric-keyvault-references.md)
