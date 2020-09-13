---
title: Konfigurace přístupu k veřejnému registru
description: Nakonfigurujte pravidla protokolu IP pro povolení přístupu ke službě Azure Container Registry z vybraných veřejných IP adres nebo rozsahů adres.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 4e4ee817e2534bcca09cba89daafd379ff3f03f0
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488758"
---
# <a name="configure-public-ip-network-rules"></a>Konfigurace pravidel sítě veřejných IP adres

Služba Azure Container Registry ve výchozím nastavení přijímá připojení přes Internet z hostitelů v libovolné síti. V tomto článku se dozvíte, jak nakonfigurovat registr kontejneru tak, aby povoloval přístup jenom pro konkrétní veřejné IP adresy nebo rozsahy adres. K dispozici jsou ekvivalentní kroky pomocí Azure CLI a Azure Portal.

V rámci veřejného koncového bodu registru jsou nakonfigurovaná pravidla sítě IP. Pravidla sítě IP se nevztahují na privátní koncové body konfigurované pomocí [privátního propojení](container-registry-private-link.md) .

Konfigurace pravidel přístupu k IP adres je dostupná na úrovni služby **Premium** Container Registry Service. Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Přístup z vybrané veřejné sítě – rozhraní příkazového řádku

### <a name="change-default-network-access-to-registry"></a>Změna výchozího přístupu k síti do registru

Chcete-li omezit přístup k vybrané veřejné síti, nejprve změňte výchozí akci na odepřít přístup. Název registru nahraďte následujícím příkazem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Přidat síťové pravidlo do registru

Pomocí příkazu [AZ ACR Network-Rule Add][az-acr-network-rule-add] přidejte do registru síťové pravidlo, které umožňuje přístup z veřejné IP adresy nebo rozsahu. Například nahraďte název registru kontejneru a veřejnou IP adresu virtuálního počítače ve virtuální síti.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Po přidání pravidla trvá několik minut, než se pravidlo projeví.

## <a name="access-from-selected-public-network---portal"></a>Přístup z vybrané veřejné sítě – portál

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení**vyberte **sítě**.
1. Na kartě **veřejný přístup** vyberte možnost povolíte veřejný přístup z **vybraných sítí**.
1. V části **Brána firewall**zadejte veřejnou IP adresu, třeba veřejnou IP adresu virtuálního počítače ve virtuální síti. Případně zadejte rozsah adres v zápisu CIDR, který obsahuje IP adresu virtuálního počítače.
1. Vyberte **Uložit**.

![Konfigurace pravidla brány firewall pro Registry kontejneru][acr-access-selected-networks]

> [!NOTE]
> Po přidání pravidla trvá několik minut, než se pravidlo projeví.

> [!TIP]
> Volitelně můžete povolit přístup k registru z místního klientského počítače nebo rozsahu IP adres. K povolení tohoto přístupu potřebujete veřejnou IPv4 adresu tohoto počítače. Tuto adresu najdete tak, že v internetovém prohlížeči vyhledáte "to je moje IP adresa". Aktuální IPv4 adresa klienta se také zobrazí automaticky při konfiguraci nastavení brány firewall na stránce **síť** na portálu.

## <a name="disable-public-network-access"></a>Zakázat přístup k veřejné síti

Volitelně zakažte veřejný koncový bod v registru. Zakázání veřejného koncového bodu přepíše všechny konfigurace brány firewall. Můžete například chtít zakázat veřejný přístup k registru zabezpečenému ve virtuální síti pomocí [privátního odkazu](container-registry-private-link.md).

> [!NOTE]
> Pokud je registr nastavený ve virtuální síti s [koncovým bodem služby](container-registry-vnet.md), zakážete přístup k veřejnému koncovému bodu registru, ale zakážete přístup k registru v rámci virtuální sítě.

### <a name="disable-public-access---cli"></a>Zakázat veřejný přístup – CLI

Pokud chcete zakázat veřejný přístup pomocí rozhraní příkazového řádku Azure, spusťte příkaz [AZ ACR Update][az-acr-update] a nastavte `--public-network-enabled` na `false` . Argument vyžaduje rozhraní příkazového `public-network-enabled` řádku Azure CLI 2.6.0 nebo novější. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Zakázat veřejný přístup – portál

1. Na portálu přejděte do registru kontejneru a vyberte **nastavení > sítě**.
1. Na kartě **veřejný přístup** vyberte v části **Povolení přístupu k veřejné síti**možnost **zakázáno**. Potom vyberte **Uložit**.

![Zakázat veřejný přístup][acr-access-disabled]


## <a name="restore-public-network-access"></a>Obnovit přístup k veřejné síti

Pokud chcete znovu povolit veřejný koncový bod, aktualizujte nastavení sítě tak, aby umožňovalo veřejný přístup. Povolení veřejného koncového bodu přepíše všechny konfigurace brány firewall. 

### <a name="restore-public-access---cli"></a>Obnovení veřejného přístupu – CLI

Spusťte příkaz [AZ ACR Update][az-acr-update] a `--public-network-enabled` nastavte `true` na. 

> [!NOTE]
> Argument vyžaduje rozhraní příkazového `public-network-enabled` řádku Azure CLI 2.6.0 nebo novější. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Obnovit veřejný přístup – portál

1. Na portálu přejděte do registru kontejneru a vyberte **nastavení > sítě**.
1. Na kartě **veřejný přístup** vyberte v části **Povolení přístupu k veřejné síti**možnost **všechny sítě**. Potom vyberte **Uložit**.

![Veřejný přístup ze všech sítí][acr-access-all-networks]

## <a name="troubleshoot"></a>Řešení potíží

Pokud je nastavené pravidlo veřejné sítě nebo je veřejný přístup k registru odepřený, pokusy o přihlášení k registru z nepovolené veřejné sítě se nezdaří. Přístup klienta z za proxy serveru HTTPS selže i v případě, že není nastavené pravidlo přístupu pro proxy server. Zobrazí se chybová zpráva podobná `Error response from daemon: login attempt failed with status: 403 Forbidden` nebo `Looks like you don't have access to registry` .

K těmto chybám může dojít také v případě, že používáte proxy server HTTPS, který je povolen pravidlem přístupu k síti, ale proxy server není správně nakonfigurován v klientském prostředí. Ověřte, že je klient Docker i démon Docker nakonfigurovaný pro chování proxy serveru. Podrobnosti najdete v dokumentaci k Docker v dokumentaci k [serveru proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) .


## <a name="next-steps"></a>Další kroky

* Pokud chcete omezit přístup k registru pomocí privátního koncového bodu ve virtuální síti, přečtěte si téma [konfigurace privátního odkazu Azure pro službu Azure Container Registry](container-registry-private-link.md).
* Pokud potřebujete nastavit pravidla přístupu k registru z za bránou firewall klienta, přečtěte si téma [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md).

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
