---
title: Přehled spravované identity Service Fabric | Microsoft Docs
description: Tento článek představuje přehled spravované identity.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: d63fd3d1b778c691d053f13fbf0fbb2ed5ccb3e3
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968285"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Spravovaná identita pro aplikaci Service Fabric (Preview)

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zabezpečení přihlašovacích údajů je důležitou úlohou, protože se nikdy nezobrazuje na pracovních stanicích pro vývojáře a nekontrolují se do správy zdrojových kódů. Tento problém řeší funkce Managed identity pro prostředky Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje služby Azure s automaticky spravovanou identitou ve službě Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Funkce Managed identity pro prostředky Azure je zdarma se službou Azure AD pro předplatné Azure. Neúčtují se žádné další náklady.

> [!NOTE]
> Spravovaná identita pro prostředky Azure je nový název služby dřív označovaný jako Identita spravované služby (MSI).

## <a name="terminology"></a>Terminologie

V sadě dokumentace spravované identity pro prostředky Azure se používají následující výrazy:

- **ID klienta** – jedinečný identifikátor generovaný službou Azure AD, který je svázán s aplikací a instančním objektem během počátečního zřizování (viz také [ID aplikace](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **ID objektu zabezpečení** – ID objektu instančního objektu pro spravovanou identitu, která se používá pro udělení přístupu na základě role k prostředku Azure.

- **Instanční** objekt – objekt Azure Active Directory, který představuje projekci aplikace AAD v daném tenantovi (viz také [instanční](../active-directory/develop/developer-glossary.md#service-principal-object)objekt).


## <a name="about-managed-identities-in-azure"></a>Spravované identity v Azure

- [Typy spravované identity (MI) v Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Jak funguje spravovaná identita přiřazená systémem v Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Jak funguje uživatelsky definovaná spravovaná identita (MI) v Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Podporované scénáře pro aplikace Service Fabric

Spravované identity pro Service Fabric se podporují jenom v clusterech nasazených Service Fabric Azure a jenom pro aplikace nasazené jako prostředky Azure. aplikacím, které nejsou nasazené jako prostředek Azure, nelze přiřadit identitu. V tomto případě se podpora spravovaných identit ve službě Azure Service Fabric cluster skládá ze dvou fází:

1. Přiřaďte prostředku aplikace jednu nebo více spravovaných identit. aplikaci lze přiřadit jedinou identitu přiřazenou systémem a/nebo až 32 uživatelsky přiřazených identit.

2. V rámci definice aplikace namapujte jednu identitu přiřazenou aplikaci na jakoukoli jednotlivou službu, která se skládá z aplikace.

Identita aplikace přiřazená systému je pro tuto aplikaci jedinečná; uživatelsky přiřazená identita je samostatný prostředek, který může být přiřazen více aplikacím. V rámci aplikace je možné přiřadit jednu identitu (ať už systémem přiřazený nebo přiřazený uživateli) k více službám aplikace, ale každé individuální službě se dá přiřadit jenom jedna identita. Služba musí mít k této funkci explicitně přiřazenou identitu. V důsledku toho mapování identit aplikace na příslušné služby umožňuje izolaci v aplikaci – služba může k ní použít jenom namapovanou identitu (a žádná vůbec, pokud ji explicitně nepřiřadila).  

Seznam podporovaných scénářů verze Preview je následující:

   - Nasazení nové aplikace s jednou nebo více službami a jednou nebo více přiřazenými identitami

   - Přiřaďte k existující aplikaci jednu nebo víc spravovaných identit, aby bylo možné získat přístup k prostředkům Azure. aplikace musí být nasazená jako samotný prostředek Azure.


Následující scénáře nejsou podporovány nebo nejsou doporučovány. Všimněte si, že tyto akce nemusí být blokované, ale můžou způsobit výpadky ve vašich aplikacích:

   - Umožňuje odebrat nebo změnit identity přiřazené k aplikaci. Pokud je nutné provést změny, odešlete samostatné nasazení, abyste přidali nové přiřazení identity a pak odebrali dříve přiřazenou identitu. Odebrání identity z existující aplikace může mít nežádoucí účinky, včetně ukončení aplikace ve stavu, který nelze upgradovat. Zcela odstraní aplikaci, pokud je nutné odebrat identitu. Všimněte si, že se odstraní identita přiřazená systémem (Pokud je definována) přidružená k aplikaci a odstraní všechna přidružení s uživatelsky přiřazenými identitami přiřazenými k aplikaci.

>
> [!NOTE]
>
> Tato funkce je ve verzi Preview. v takovém případě se může vztahovat k častým změnám a nemusí být vhodná pro produkční nasazení.

## <a name="next-steps"></a>Další postup
* [Nasazení nového clusteru Azure Service Fabric s podporou spravované identity](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Využití spravované identity Service Fabric aplikace z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)
