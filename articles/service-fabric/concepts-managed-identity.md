---
title: Spravované identity pro Azure
description: Seznamte se s používáním spravovaných identit pro Azure s Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881716"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Použití spravovaných identit pro Azure s Service Fabric

Běžným problémem při sestavování cloudových aplikací je způsob, jak bezpečně spravovat přihlašovací údaje v kódu pro ověřování v různých službách bez jejich uložení lokálně na pracovní stanici pro vývojáře nebo ve správě zdrojového kódu. *Spravované identity pro Azure* vyřeší tento problém pro všechny prostředky v Azure Active Directory (Azure AD) tím, že jim poskytnou automaticky spravované identity v rámci Azure AD. Identitu služby můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně Key Vault, bez jakýchkoli přihlašovacích údajů uložených v kódu.

*Spravované identity pro prostředky Azure* jsou bezplatné s Azure AD pro předplatné Azure. Neúčtují se za ní žádné další poplatky.

> [!NOTE]
> *Spravované identity pro Azure* jsou novým názvem služby, který se dřív jmenoval jako identita spravované služby (MSI).

## <a name="concepts"></a>Koncepty

Spravované identity pro Azure jsou založené na několika klíčových konceptech:

- **ID klienta** – jedinečný identifikátor generovaný službou Azure AD, který je svázán s aplikací a instančním objektem během počátečního zřizování (viz také [ID aplikace](../active-directory/develop/developer-glossary.md#application-id-client-id).)

- **ID objektu zabezpečení** – ID objektu instančního objektu pro spravovanou identitu, která se používá pro udělení přístupu na základě role k prostředku Azure.

- **Instanční** objekt – objekt Azure Active Directory, který představuje projekci aplikace AAD v daném tenantovi (viz také [instanční](../active-directory/develop/developer-glossary.md#service-principal-object)objekt).

Existují dva typy spravovaných identit:

- **Spravovaná identita přiřazená systémem** je povolena přímo v instanci služby Azure.  Životní cyklus identity přiřazené systémem je jedinečný pro instanci služby Azure, na které je povolený.
- **Spravovaná identita přiřazená uživatelem** se vytváří jako samostatný prostředek Azure. Identitu je možné přiřadit k jedné nebo více instancím služby Azure a spravují se odděleně od životních cyklů těchto instancí.

Další informace o rozdílu mezi spravovanými typy identit najdete v tématu [Jak fungují spravované identity prostředků Azure?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

## <a name="supported-scenarios-for-service-fabric-applications"></a>Podporované scénáře pro aplikace Service Fabric

Spravované identity pro Service Fabric se podporují jenom v clusterech Service Fabric nasazených v Azure a jenom pro aplikace nasazené jako prostředky Azure. aplikaci, která není nasazena jako prostředek Azure, nelze přiřadit identitu. V tomto případě se podpora spravovaných identit v clusteru Azure Service Fabric skládá ze dvou fází:

1. Přiřaďte prostředku aplikace jednu nebo více spravovaných identit. aplikaci lze přiřadit jedinou identitu přiřazenou systémem a/nebo až 32 uživatelsky přiřazených identit.

2. V rámci definice aplikace namapujte jednu identitu přiřazenou aplikaci na jakoukoli jednotlivou službu, která se skládá z aplikace.

Identita aplikace přiřazená systému je pro tuto aplikaci jedinečná; uživatelsky přiřazená identita je samostatný prostředek, který může být přiřazen více aplikacím. V rámci aplikace je možné přiřadit jednu identitu (ať už systémem přiřazený nebo přiřazený uživateli) k více službám aplikace, ale každé individuální službě se dá přiřadit jenom jedna identita. Služba musí mít k této funkci explicitně přiřazenou identitu. V důsledku toho mapování identit aplikace na příslušné služby umožňuje izolaci v aplikaci – služba může použít pouze namapovanou identitu.  

V současné době jsou pro tuto funkci podporovány následující scénáře:

- Nasazení nové aplikace s jednou nebo více službami a s jednou nebo více přiřazenými identitami

- Přiřazení jedné nebo více spravovaných identit existující aplikaci (nasazené v Azure) za účelem přístupu k prostředkům Azure

Následující scénáře nejsou podporovány nebo nejsou doporučovány. Všimněte si, že tyto akce nemusí být blokované, ale můžou způsobit výpadky ve vašich aplikacích:

- Umožňuje odebrat nebo změnit identity přiřazené k aplikaci. Pokud je nutné provést změny, odešlete samostatné nasazení, abyste přidali nové přiřazení identity a pak odebrali dříve přiřazenou identitu. Odebrání identity z existující aplikace může mít nežádoucí účinky, včetně ukončení aplikace ve stavu, který nelze upgradovat. Zcela odstraní aplikaci, pokud je nutné odebrat identitu. Všimněte si, že se odstraní identita přiřazená systémem (Pokud je definována) přidružená k aplikaci a odstraní všechna přidružení s uživatelsky přiřazenými identitami přiřazenými k aplikaci.

- Service Fabric podpora spravovaných identit v tuto chvíli není integrována do [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication).

## <a name="next-steps"></a>Další kroky

- [Nasazení nového clusteru Azure Service Fabric s podporou spravované identity](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Využití spravované identity Service Fabric aplikace z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
- [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)
- [Deklarace a používání tajných klíčů aplikace jako KeyVaultReferences](./service-fabric-keyvault-references.md)