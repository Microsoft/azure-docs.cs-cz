---
title: Rychlý Start – zřízení a aktivace modulu HSM spravovaného službou Azure
description: Rychlý Start ukazující, jak zřídit a aktivovat spravovaný modul HSM pomocí Azure CLI
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756809"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI

Azure Key Vault spravovaný modul hardwarového zabezpečení (HSM) je plně spravovaná cloudová služba s vysokou dostupností, která vyhovuje standardům, která umožňuje chránit kryptografické klíče pro vaše cloudové aplikace, a to pomocí ověřené HSM **úrovně 3 na úrovni Standard FIPS 140-2** . Další informace o spravovaném modulu HSM si můžete projít v [přehledu](overview.md). 

V tomto rychlém startu vytvoříte a aktivujete spravovaný modul HSM pomocí Azure CLI. Po jeho vytvoření uložíte tajný klíč.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Spravovaný modul HSM v rámci vašeho předplatného. Informace najdete v tématu [rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI](quick-create-cli.md) , který umožňuje zřídit a aktivovat spravovaný modul HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *ContosoResourceGroup* v umístění *eastus2* .

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Vytvoření spravovaného modulu HSM

Vytvoření spravovaného modulu HSM je proces dvou kroků:
1. Zřídí spravovaný prostředek HSM.
1. Aktivujte spravovaný modul HSM stažením domény zabezpečení.

### <a name="provision-a-managed-hsm"></a>Zřízení spravovaného modulu HSM

Pomocí `az keyvault create` příkazu vytvořte spravovaný modul HSM. Tento skript má tři povinné parametry: název skupiny prostředků, název HSM a geografické umístění.

Pro vytvoření spravovaného prostředku HSM musíte zadat následující vstupy:
- Skupina prostředků, do které se umístí do vašeho předplatného.
- Umístění Azure.
- Seznam počátečních správců.

Následující příklad vytvoří modul HARDWAROVÉho zabezpečení s názvem **ContosoMHSM**ve skupině prostředků  **ContosoResourceGroup**umístěný v umístění **východní USA 2** s **aktuálně přihlášeným uživatelem** jako jediný správce.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Příkaz Create může trvat několik minut. Po úspěšném vrácení budete připraveni aktivovat modul hardwarového zabezpečení (HSM).

Výstup tohoto příkazu zobrazuje vlastnosti spravovaného modulu HSM, který jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **název**: v tomto příkladu je název ContosoMHSM. Tento název použijete pro jiné Key Vault příkazy.
* **hsmUri**: v tomto příkladu je identifikátor URI ' https://contosohsm.managedhsm.azure.net . ' Aplikace, které používají váš modul HARDWAROVÉho zabezpečení prostřednictvím REST API, musí používat tento identifikátor URI.

Váš účet Azure je teď autorizovaný k provádění operací s tímto spravovaným modulem HSM. Od tohoto konce je nikdo jiný autorizovaný.

### <a name="activate-your-managed-hsm"></a>Aktivace spravovaného modulu HSM

Všechny příkazy roviny dat jsou zakázané, dokud se neaktivuje modul HARDWAROVÉho zabezpečení. Nebudete moci vytvářet klíče ani role přiřadit. Modul hardwarového zabezpečení (HSM) mohou aktivovat pouze určení správci, kteří byli přiřazeni během příkazu CREATE. Chcete-li aktivovat modul HSM, musíte si stáhnout [doménu zabezpečení](security-domain.md).

Chcete-li aktivovat modul HARDWAROVÉho zabezpečení, který potřebujete:
- Minimální 3 páry klíčů RSA (maximum 10)
- Zadejte minimální počet klíčů nutný k dešifrování domény zabezpečení (kvorum).

Pokud chcete aktivovat modul hardwarového zabezpečení (HSM), odešlete aspoň 3 (maximum 10) veřejných klíčů RSA do modulu HSM. Modul HARDWAROVÉho zabezpečení zabezpečuje pomocí těchto klíčů doménu zabezpečení a odesílá je zpátky. Po úspěšném dokončení této domény zabezpečení je váš modul HARDWAROVÉho zabezpečení připravený k použití. Je také nutné zadat kvorum, což je minimální počet privátních klíčů vyžadovaných k dešifrování domény zabezpečení.

Následující příklad ukazuje, jak použít  `openssl` ke generování 3 certifikátu podepsaného svým vlastníkem.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Vytvořte a bezpečně uložte páry klíčů RSA a soubor domény zabezpečení vygenerované v tomto kroku.

`az keyvault security-domain download`K stažení domény zabezpečení a aktivaci spravovaného modulu HSM použijte příkaz. Níže uvedený příklad používá 3 páry klíčů RSA (pro tento příkaz jsou nutné pouze veřejné klíče) a nastavuje kvorum na 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Uložte prosím zabezpečený soubor domény zabezpečení a páry klíčů RSA. Budete je potřebovat pro zotavení po havárii nebo pro vytvoření jiného spravovaného modulu HSM, který sdílí stejnou doménu zabezpečení, takže může sdílet klíče.

Po úspěšném stažení domény zabezpečení bude modul HSM v aktivním stavu a bude připravený k použití.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group). Prostředky můžete odstranit následujícím způsobem:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [Přehled spravovaného HSM](overview.md)
- Další informace o [správě klíčů ve spravovaném modulu HSM](key-management.md)
- Projděte si spravované osvědčené [postupy modulu HSM](best-practices.md)
