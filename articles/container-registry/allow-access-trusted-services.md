---
title: Přístup k registru s omezeným přístupem přes síť pomocí důvěryhodné služby Azure
description: Povolení důvěryhodné instance služby Azure pro zabezpečený přístup k registru kontejneru omezeného na síť pro vyžádání nebo vložení imagí
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 3cc32630ea689891e7ba75163c33bc499a38becd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716478"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Povoluje důvěryhodným službám zabezpečený přístup k registru kontejnerů s omezeným přístupem k síti (Preview).

Azure Container Registry může vybrat důvěryhodné služby Azure pro přístup k registru, který je nakonfigurovaný s pravidly přístupu k síti. Pokud jsou povolené důvěryhodné služby, může instance důvěryhodné služby bezpečně obejít Síťová pravidla registru a provádět operace, jako jsou vyžádané nebo nabízené image. Spravovaná identita instance služby se používá pro přístup a je nutné jí přiřadit roli Azure a ověřit ji pomocí registru.

Příklady příkazů v tomto článku můžete spustit pomocí Azure Cloud Shell nebo místní instalace rozhraní příkazového řádku Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2,18 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Povolení přístupu k registru pro důvěryhodné služby Azure je funkce ve **verzi Preview** .

## <a name="limitations"></a>Omezení

* Aby bylo možné získat přístup k registru kontejneru omezeného na síť, musíte použít spravovanou identitu přiřazenou systémem povolenou v [důvěryhodné službě](#trusted-services) . Uživatelsky přiřazené spravované identity nejsou aktuálně podporovány.
* Povolení důvěryhodných služeb se nevztahuje na registr kontejnerů nakonfigurovaný s [koncovým bodem služby](container-registry-vnet.md). Tato funkce ovlivňuje jenom Registry, které jsou omezené pomocí [privátního koncového bodu](container-registry-private-link.md) nebo které mají použít [pravidla přístupu k veřejným IP adresám](container-registry-access-selected-networks.md) . 

## <a name="about-trusted-services"></a>O důvěryhodných službách

Azure Container Registry má vrstvený model zabezpečení, který podporuje více konfigurací sítě, které omezují přístup k registru, včetně:

* [Privátní koncový bod s privátním propojením Azure](container-registry-private-link.md). Po nakonfigurování je privátní koncový bod registru přístupný jenom pro prostředky v rámci virtuální sítě pomocí privátních IP adres.  
* [Pravidla brány firewall registru](container-registry-access-selected-networks.md), která umožňují přístup k veřejnému koncovému bodu registru jenom z konkrétních veřejných IP adres nebo rozsahů adres. Bránu firewall můžete také nakonfigurovat tak, aby při používání privátních koncových bodů blokovala veškerý přístup k veřejnému koncovému bodu.

Při nasazení ve virtuální síti nebo nakonfigurované pomocí pravidel brány firewall registr zakáže přístup ve výchozím nastavení pro uživatele nebo služby mimo tyto zdroje. 

Několik služeb Azure s více klienty funguje z sítí, které není možné zahrnout do těchto nastavení sítě registru, a brání tak jejich nahrání nebo nahrání imagí do registru. Když označíte některé instance služby jako důvěryhodné, vlastník registru může dovolit, aby se pomocí vybraných prostředků Azure bezpečně vynechal nastavení sítě v registru pro vyžádání nebo vložení imagí. 

### <a name="trusted-services"></a>Důvěryhodné služby

Pokud je povolené nastavení **Povolit důvěryhodné služby** registru, můžou instance těchto služeb získat přístup k registru kontejnerů s omezeným přístupem k síti (výchozí nastavení). Další služby budou v průběhu času přidány.

|Důvěryhodná služba  |Podporované scénáře použití  |
|---------|---------|
|Úlohy ACR     | [Přístup k jinému registru z ACR úlohy](container-registry-tasks-cross-registry-authentication.md)       |
|Azure Container Registry | [Import imagí z jiného služby Azure Container Registry](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Který, povolení nastavení Povolit důvěryhodné služby neumožňuje instancím jiných spravovaných služeb Azure, včetně App Service, Azure Container Instances a Azure Security Center pro přístup k registru kontejneru omezeného na síť.

## <a name="allow-trusted-services---cli"></a>Povolení důvěryhodných služeb – CLI

Ve výchozím nastavení je nastavení Povolit důvěryhodné služby povolené v novém službě Azure Container Registry. Zakažte nebo povolte nastavení spuštěním příkazu [AZ ACR Update](/cli/azure/acr#az-acr-update) .

Chcete-li zakázat:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Povolení nastavení v existujícím registru nebo v registru, ve kterém je už zakázané:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Povolení důvěryhodných služeb – portál

Ve výchozím nastavení je nastavení Povolit důvěryhodné služby povolené v novém službě Azure Container Registry. 

Zakázání nebo opětovné povolení nastavení na portálu:

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení** vyberte **sítě**. 
1. V nástroji **povolí přístup k veřejné síti** vyberte **vybrané sítě** nebo **zakázáno**.
1. Proveďte některou z následujících akcí:
    * Pokud chcete zakázat přístup pomocí důvěryhodných služeb, zrušte v části **výjimka brány firewall** **možnost Povolit důvěryhodným službám Microsoftu přístup k tomuto registru kontejneru**. 
    * Chcete-li u důvěryhodných služeb povolený přístup k tomuto registru kontejneru, zaškrtněte v části **výjimka brány firewall** **možnost dovolit důvěryhodným službám Microsoftu přístup**
1. Vyberte **Uložit**.

## <a name="trusted-services-workflow"></a>Pracovní postup důvěryhodných služeb

Tady je typický pracovní postup, který umožňuje instanci důvěryhodné služby získat přístup k registru kontejneru omezeného na síť.

1. Povolte spravovanou identitu přiřazenou systémem [pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) v instanci jedné z [důvěryhodných služeb](#trusted-services) pro Azure Container Registry.
1. Přiřaďte k registru identitu [role Azure](container-registry-roles.md) . Například přiřaďte roli ACRPull k vyžádání imagí kontejneru.
1. V registru s omezeným přístupem k síti nakonfigurujte nastavení tak, aby umožňovalo přístup pro důvěryhodné služby.
1. Použijte přihlašovací údaje identity k ověření pomocí registru s omezením sítě. 
1. Vyžádat image z registru nebo provést jiné operace, které role povoluje.

### <a name="example-acr-tasks"></a>Příklad: ACR úkoly

Následující příklad ukazuje použití úloh ACR jako důvěryhodné služby. Podrobnosti o úlohách najdete [v tématu ověřování mezi registry v úloze ACR s využitím identity spravované službou Azure](container-registry-tasks-cross-registry-authentication.md) .

1. Vytvořte nebo aktualizujte službu Azure Container registry a nahrajte do registru [ukázkovou základní image](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) . Tento registr je *základní registr* pro daný scénář.
1. V druhém registru kontejnerů Azure [Definujte](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) a [vytvořte](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) úlohu ACR, která načte image ze základního registru. Při vytváření úlohy povolte spravovanou identitu přiřazenou systémem.
1. Přiřaďte k základnímu registru identitu úlohy, kterou [má role Azure](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Například přiřaďte roli AcrPull, která má oprávnění k vyžádání imagí.
1. [Přidejte do úlohy pověření spravované identity](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) .
1. Pokud chcete potvrdit, že úloha obchází omezení sítě, [zakažte veřejný přístup](container-registry-access-selected-networks.md#disable-public-network-access) v základním registru.
1. Spusťte úlohu. Pokud je základní registr a úloha správně nakonfigurované, úloha se úspěšně spustí, protože základní registr umožňuje přístup.

Chcete-li otestovat zakázání přístupu pomocí důvěryhodných služeb:

1. V základním registru zakažte nastavení tak, aby povolovala přístup pro důvěryhodné služby.
1. Spusťte úlohu znovu. V takovém případě se úloha nezdařila, protože základní registr již neumožňuje přístup k tomuto úkolu.

## <a name="next-steps"></a>Další kroky

* Pokud chcete omezit přístup k registru pomocí privátního koncového bodu ve virtuální síti, přečtěte si téma [konfigurace privátního odkazu Azure pro službu Azure Container Registry](container-registry-private-link.md).
* Pokud chcete nastavit pravidla brány firewall registru, přečtěte si téma [Konfigurace pravidel sítě veřejných IP adres](container-registry-access-selected-networks.md).
