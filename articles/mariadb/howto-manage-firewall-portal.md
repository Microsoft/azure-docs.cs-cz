---
title: Správa pravidel brány firewall-Azure Portal-Azure Database for MariaDB
description: Vytváření a Správa Azure Database for MariaDB pravidel brány firewall pomocí Azure Portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 464b13fe4aa727a4d3ca3ff074b8bb94544576a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86101218"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Vytváření a Správa Azure Database for MariaDB pravidel brány firewall pomocí Azure Portal
Pravidla brány firewall na úrovni serveru se dají použít ke správě přístupu k serveru Azure Database for MariaDB ze zadané IP adresy nebo rozsahu IP adres.

Pravidla Virtual Network (VNet) je možné použít také k zabezpečení přístupu k serveru. Přečtěte si další informace o [vytváření a správě Virtual Networkch koncových bodů a pravidel služby pomocí Azure Portal](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

1. Na stránce MariaDB serveru klikněte v části nastavení na **zabezpečení připojení** a otevřete stránku zabezpečení připojení pro Azure Database for MariaDB.

   ![Azure Portal – kliknutí na zabezpečení připojení](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Na panelu nástrojů klikněte na **Přidat moji IP adresu** . Tím se automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak je znázorněno v systému Azure.

   ![Azure Portal klikněte na Přidat moji IP adresu.](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Před uložením konfigurace ověřte svoji IP adresu. V některých situacích se IP adresa zjištěná Azure Portal liší od IP adresy používané při přístupu k Internetu a k serverům Azure. Proto může být nutné změnit počáteční IP adresu a koncovou IP adresu, aby funkce pravidla fungovala podle očekávání.

   Použijte vyhledávací modul nebo jiný online nástroj ke kontrole vlastní IP adresy. Vyhledejte například "Co je moje IP adresa".

4. Přidejte další rozsahy adres. V pravidlech brány firewall pro Azure Database for MariaDB můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete pravidlo omezit na jednu IP adresu, zadejte stejnou adresu do polí Počáteční IP adresa a koncová IP adresa. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k libovolné databázi na serveru MariaDB, ke které mají platné přihlašovací údaje.

   ![Azure Portal – pravidla brány firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Kliknutím na **Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Počkejte, až se potvrdí, že aktualizace pravidel firewallu proběhla úspěšně.

   ![Azure Portal – klikněte na Uložit.](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete aplikacím z Azure povolit připojení k vašemu Azure Database for MariaDB serveru, musí být povolená připojení Azure. Například pro hostování aplikace Web Apps v Azure nebo aplikace, která běží na virtuálním počítači Azure, nebo pro připojení z Azure Data Factory brány pro správu dat. Aby se tato připojení dala povolit, nemusíte být prostředky ve stejné Virtual Network (virtuální síti) nebo skupině prostředků pro pravidlo brány firewall. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. K dispozici je několik metod, jak tyto typy připojení povolit. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete na portálu v podokně **zabezpečení připojení** nastavit možnost **Povolení přístupu ke službám Azure** na **zapnuto** a klikněte na **Uložit**. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne serveru Azure Database for MariaDB.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Spravovat existující pravidla brány firewall v Azure Portal
Opakováním kroků spravujte pravidla brány firewall.
* Chcete-li přidat aktuální počítač, klikněte na tlačítko **+ Přidat moji IP adresu**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete přidat další IP adresy, zadejte **název pravidla**, **Počáteční IP adresu**a **koncovou IP adresu**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole v pravidle a pak na Upravit. Kliknutím na **Uložit** uložte změny.
* Pokud chcete odstranit stávající pravidlo, klikněte na tlačítko se třemi tečkami [...] a pak klikněte na **Odstranit**. Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další kroky
 - Podobně můžete vytvořit skript pro [vytváření a správu pravidel brány firewall Azure Database for MariaDB pomocí Azure CLI](howto-manage-firewall-cli.md).
 - [Vytvořením a správou koncových bodů a pravidel služby Virtual Network a pomocí Azure Portal](howto-manage-vnet-portal.md)můžete lépe zabezpečit přístup k vašemu serveru.