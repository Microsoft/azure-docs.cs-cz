---
title: Správa pravidel brány firewall-Azure Portal-Azure Database for MySQL
description: Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7c5bc010653a936c00c5995142b5b34829591d24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884692"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal
Pravidla brány firewall na úrovni serveru se dají použít ke správě přístupu k serveru Azure Database for MySQL ze zadané IP adresy nebo rozsahu IP adres. 

Pravidla Virtual Network (VNet) je možné použít také k zabezpečení přístupu k serveru. Přečtěte si další informace o [vytváření a správě Virtual Networkch koncových bodů a pravidel služby pomocí Azure Portal](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

1. Na stránce serveru MySQL klikněte v části nastavení na **zabezpečení připojení** a otevřete stránku zabezpečení připojení pro Azure Database for MySQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal – kliknutí na zabezpečení připojení":::

2. Na panelu nástrojů klikněte na **Přidat moji IP adresu** . Tím se automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak je znázorněno v systému Azure.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal klikněte na Přidat moji IP adresu.":::

3. Před uložením konfigurace ověřte svoji IP adresu. V některých situacích se IP adresa zjištěná Azure Portal liší od IP adresy používané při přístupu k Internetu a k serverům Azure. Proto může být nutné změnit počáteční IP adresu a koncovou IP adresu, aby funkce pravidla fungovala podle očekávání.

   Použijte vyhledávací modul nebo jiný online nástroj ke kontrole vlastní IP adresy. Vyhledejte například "Co je moje IP adresa".

4. Přidejte další rozsahy adres. V pravidlech brány firewall pro Azure Database for MySQL můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete pravidlo omezit na jednu IP adresu, zadejte stejnou adresu do polí Počáteční IP adresa a koncová IP adresa. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k jakékoli databázi na serveru MySQL, ke které mají platné přihlašovací údaje.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal – pravidla brány firewall":::

5. Kliknutím na **Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Počkejte, až se potvrdí, že aktualizace pravidel firewallu proběhla úspěšně.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal – klikněte na Uložit.":::

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete aplikacím z Azure povolit připojení k vašemu Azure Database for MySQL serveru, musí být povolená připojení Azure. Například pro hostování aplikace Web Apps v Azure nebo aplikace, která běží na virtuálním počítači Azure, nebo pro připojení z Azure Data Factory brány pro správu dat. Aby se tato připojení dala povolit, nemusíte být prostředky ve stejné Virtual Network (virtuální síti) nebo skupině prostředků pro pravidlo brány firewall. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. K dispozici je několik metod, jak tyto typy připojení povolit. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete na portálu v podokně **zabezpečení připojení** nastavit možnost **Povolení přístupu ke službám Azure** na **zapnuto** a potom kliknout na **Uložit**. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne serveru Azure Database for MySQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Spravujte stávající pravidla brány firewall na úrovni serveru pomocí Azure Portal
Opakováním kroků spravujte pravidla brány firewall.
* Chcete-li přidat aktuální počítač, klikněte na tlačítko **+ Přidat moji IP adresu**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete přidat další IP adresy, zadejte **název pravidla**, **Počáteční IP adresu**a **koncovou IP adresu**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole v pravidle a pak na Upravit. Kliknutím na **Uložit** uložte změny.
* Pokud chcete odstranit stávající pravidlo, klikněte na tlačítko se třemi tečkami [...] a pak klikněte na **Odstranit**. Kliknutím na **Uložit** uložte změny.


## <a name="next-steps"></a>Další kroky
- Podobně můžete vytvořit skript pro [vytváření a správu pravidel brány firewall Azure Database for MySQL pomocí Azure CLI](howto-manage-firewall-using-cli.md).
- [Vytvořením a správou koncových bodů a pravidel služby Virtual Network a pomocí Azure Portal](howto-manage-vnet-using-portal.md)můžete lépe zabezpečit přístup k vašemu serveru.
- Nápovědu k připojení k serveru Azure Database for MySQL najdete v tématu [knihovny připojení pro Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
