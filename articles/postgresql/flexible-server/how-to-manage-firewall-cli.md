---
title: Správa pravidel brány firewall – Azure CLI – Azure Database for PostgreSQL – flexibilní Server
description: Vytvoření a Správa pravidel brány firewall pro Azure Database for PostgreSQL-flexibilní Server pomocí příkazového řádku Azure CLI.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 36249694c5a4de8a738853892f827c6d9e1e4aff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489468"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Vytváření a Správa Azure Database for PostgreSQL – flexibilní pravidla brány firewall serveru pomocí rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní Server podporuje dva typy vzájemně se vylučujících metod síťového připojení, které se připojují k flexibilnímu serveru. Tyto dvě možnosti jsou následující:

* Veřejný přístup (povolené IP adresy)
* Privátní přístup (integrace virtuální sítě)

V tomto článku se zaměříme na vytvoření serveru PostgreSQL s **veřejným přístupem (s povolenými IP adresami)** pomocí rozhraní příkazového řádku Azure a získáte přehled příkazů Azure CLI, pomocí kterých můžete pravidla brány firewall vytvořit, aktualizovat, odstranit, vypsat a zobrazit, a to po vytvoření serveru. Díky *veřejnému přístupu (povoleným IP adresám)* se připojení k serveru PostgreSQL omezují jenom na povolená IP adresa. IP adresy klienta musí být povoleny v pravidlech brány firewall. Další informace o této službě najdete v tématu [veřejný přístup (povolené IP adresy)](./concepts-networking.md#public-access-allowed-ip-addresses). Pravidla brány firewall lze definovat v době vytváření serveru (doporučeno), ale lze je také přidat později.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Můžete také otevřít Cloud Shell na samostatné kartě prohlížeče, a to tak, že kliknete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a vyberte **ENTER** pro spuštění.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Předpoklady

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account#az-account-set) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Vytvoření pravidla brány firewall během flexibilního vytvoření serveru pomocí Azure CLI

Příkaz můžete použít `az postgres flexible-server --public access` k vytvoření flexibilního serveru s *veřejným přístupem (s povolenými IP adresami)* a ke konfiguraci pravidel brány firewall během vytváření flexibilního serveru. K poskytnutí povolených IP adres, které se budou moct připojit k serveru, můžete použít přepínač **--Public-Access** . Můžete zadat jednu nebo rozsah IP adres, které se mají zahrnout do seznamu povolených IP adres. Rozsah IP adres musí být oddělen pomlčkou a nesmí obsahovat žádné mezery. K dispozici jsou různé možnosti vytvoření flexibilního serveru pomocí rozhraní příkazového řádku, jak je znázorněno v následujícím příkladu.

Informace najdete v referenční dokumentaci k Azure CLI. <!--FIXME --> Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku. V následujících příkazech můžete například volitelně zadat skupinu prostředků.

- Vytvořte flexibilní Server s veřejným přístupem a přidejte IP adresu klienta, abyste měli přístup k serveru.
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Vytvořte flexibilní Server s veřejným přístupem a přidejte rozsah IP adres, abyste měli přístup k tomuto serveru.

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Vytvoření flexibilního serveru s veřejným přístupem a povolení aplikace z IP adres Azure pro připojení k flexibilnímu serveru
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Tato možnost nakonfiguruje bránu firewall tak, aby povolovala veřejný přístup ze služeb Azure a prostředků v rámci Azure do tohoto serveru, včetně připojení z předplatných jiných zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
    >
- - Vytvoření flexibilního serveru s veřejným přístupem a povolení všech IP adres
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Výše uvedený příkaz vytvoří pravidlo brány firewall s počáteční IP adresou = 0.0.0.0, koncovou IP adresou = 255.255.255.255 a žádné IP adresy nebudou blokované. K tomuto serveru mají přístup všichni hostitelé v Internetu. Toto pravidlo se důrazně doporučuje používat jenom dočasně a jenom na testovacích serverech, které neobsahují citlivá data.
- Vytvoření flexibilního serveru s veřejným přístupem a bez IP adresy
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > Nedoporučujeme vytvářet Server bez pravidel brány firewall. Pokud nepřidáte žádná pravidla brány firewall, nebude se moct žádný klient připojit k serveru.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Vytvoření a Správa pravidla brány firewall po vytvoření serveru
Příkaz **AZ Postgres flexibilní-server firewall-Rule** se používá v Azure CLI k vytváření, odstraňování, vypsání, zobrazení a aktualizaci pravidel firewallu.

Příkaz
- **vytvořit**: Vytvořte flexibilní pravidlo brány firewall serveru.
- **seznam**: vypíše flexibilní pravidla brány firewall serveru.
- **aktualizace**: aktualizujte pravidlo brány firewall flexibilního serveru.
- **Zobrazit**: zobrazí podrobnosti pravidla brány firewall flexibilního serveru.
- **Odstranit**: odstranění flexibilního pravidla brány firewall serveru.

Informace najdete v referenční dokumentaci k Azure CLI. <!--FIXME --> Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku. V následujících příkazech můžete například volitelně zadat skupinu prostředků.

### <a name="create-a-firewall-rule"></a>Vytvoření pravidla brány firewall
Pomocí `az postgres flexible-server firewall-rule create` příkazu vytvořte nové pravidlo brány firewall na serveru.
Pokud chcete umožnit přístup k rozsahu IP adres, zadejte IP adresu jako počáteční IP adresu a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Chcete-li umožnit přístup pro jednu IP adresu, stačí zadat jednu IP adresu, jako v tomto příkladu.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Pokud chcete aplikacím z IP adres Azure umožnit připojení k flexibilnímu serveru, zadejte IP adresu 0.0.0.0 jako počáteční IP adresu, jako v tomto příkladu.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Tato možnost nakonfiguruje bránu firewall tak, aby povolovala veřejný přístup ze služeb Azure a prostředků v rámci Azure do tohoto serveru, včetně připojení z předplatných jiných zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 
Po úspěšném vytvoření výstupního příkazu pro vytváření se zobrazí podrobnosti o vytvořeném pravidlu brány firewall ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy.

### <a name="list-firewall-rules"></a>Vypsat pravidla brány firewall 
Pomocí `az postgres flexible-server firewall-rule list` příkazu můžete vypsat existující pravidla brány firewall serveru na serveru. Všimněte si, že atribut název serveru je zadán v přepínači **--Name** . 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
Výstup obsahuje seznam pravidel (pokud existuje) ve formátu JSON (ve výchozím nastavení). Můžete použít přepínač--Output * * pro výstup výsledků do čitelnějšího formátu tabulky.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Aktualizace pravidla brány firewall
Pomocí `az postgres flexible-server firewall-rule update` příkazu aktualizujte stávající pravidlo brány firewall na serveru. Zadejte název existujícího pravidla brány firewall jako vstup a také počáteční IP adresu a atributy koncové IP adresy, které chcete aktualizovat.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po úspěchu příkaz zobrazí výstup pravidla brány firewall, které jste aktualizovali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy.

> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, pravidlo se vytvoří pomocí příkazu Update.
### <a name="show-firewall-rule-details"></a>Zobrazit podrobnosti pravidla brány firewall
Pomocí `az postgres flexible-server firewall-rule show` příkazu můžete zobrazit existující Podrobnosti pravidla brány firewall ze serveru. Zadejte název existujícího pravidla brány firewall jako vstup.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Po úspěšném provedení příkazu zobrazí výstup příkazu Podrobnosti pravidla brány firewall, které jste zadali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy.

### <a name="delete-a-firewall-rule"></a>Odstranění pravidla firewallu
Pomocí `az postgres flexible-server firewall-rule delete` příkazu odstraňte existující pravidlo brány firewall ze serveru. Zadejte název existujícího pravidla brány firewall.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Po úspěšném provedení neproběhne žádný výstup. Po selhání se text chybové zprávy zobrazí.

## <a name="next-steps"></a>Další kroky
- Další informace o [sítích v Azure Database for PostgreSQL-flexibilním serveru](./concepts-networking.md)
- Další informace o [Azure Database for PostgreSQL – flexibilní pravidla brány firewall serveru](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Vytvářejte a spravujte Azure Database for PostgreSQL – flexibilní pravidla brány firewall serveru pomocí Azure Portal](./how-to-manage-firewall-portal.md).
