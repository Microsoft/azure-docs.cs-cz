---
title: Správa pravidel brány firewall-Azure Portal-Azure Database for PostgreSQL-Single server
description: Vytvoření a Správa pravidel brány firewall pro Azure Database for PostgreSQL s jedním serverem pomocí Azure Portal
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: b2cb6a5378afac74c971ba5429775782723bef09
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882086"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Vytvoření a Správa pravidel brány firewall pro Azure Database for PostgreSQL s jedním serverem pomocí Azure Portal
Pravidla brány firewall na úrovni serveru se dají použít ke správě přístupu k serveru Azure Database for PostgreSQL ze zadané IP adresy nebo rozsahu IP adres.

Pravidla Virtual Network (VNet) je možné použít také k zabezpečení přístupu k serveru. Přečtěte si další informace o [vytváření a správě Virtual Networkch koncových bodů a pravidel služby pomocí Azure Portal](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- Server [vytvoří Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal
1. Na stránce PostgreSQL serveru klikněte v části nastavení na **zabezpečení připojení** a otevřete stránku zabezpečení připojení pro Azure Database for PostgreSQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal – kliknutí na zabezpečení připojení":::

2. Na panelu nástrojů klikněte na **Přidat moji IP adresu** . Tím se automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak je znázorněno v systému Azure.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal klikněte na Přidat moji IP adresu.":::

3. Před uložením konfigurace ověřte svoji IP adresu. V některých situacích se IP adresa zjištěná Azure Portal liší od IP adresy používané při přístupu k Internetu a k serverům Azure. Proto může být nutné změnit počáteční IP adresu a koncovou IP adresu, aby funkce pravidla fungovala podle očekávání.
   Použijte vyhledávací modul nebo jiný online nástroj ke kontrole vlastní IP adresy. Vyhledejte například "Co je moje IP adresa".

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Vyhledávání Bingu pro co je moje IP adresa":::

4. Přidejte další rozsahy adres. V pravidlech brány firewall pro Azure Database for PostgreSQL můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete pravidlo omezit na jednu IP adresu, zadejte do pole stejnou adresu jako počáteční IP adresa a koncová IP adresa. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k libovolné databázi na serveru PostgreSQL, ke které mají platné přihlašovací údaje.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal – pravidla brány firewall":::

5. Kliknutím na **Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Počkejte, až se potvrdí, že aktualizace pravidel firewallu proběhla úspěšně.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal – klikněte na Uložit.":::

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete aplikacím z Azure povolit připojení k vašemu Azure Database for PostgreSQL serveru, musí být povolená připojení Azure. Například pro hostování aplikace Web Apps v Azure nebo aplikace, která běží na virtuálním počítači Azure, nebo pro připojení z Azure Data Factory brány pro správu dat. Aby se tato připojení dala povolit, nemusíte být prostředky ve stejné Virtual Network (virtuální síti) nebo skupině prostředků pro pravidlo brány firewall. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. K dispozici je několik metod, jak tyto typy připojení povolit. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete na portálu v podokně **zabezpečení připojení** nastavit možnost **Povolení přístupu ke službám Azure** na **zapnuto** a potom kliknout na **Uložit**. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne serveru Azure Database for PostgreSQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Správa stávajících pravidel brány firewall na úrovni serveru na webu Azure Portal
Opakováním kroků spravujte pravidla brány firewall.
* Chcete-li přidat aktuální počítač, klikněte na tlačítko a **přidejte IP adresu**. Kliknutím na **Uložit** uložte změny.
* Pro přidání dalších IP adres zadejte Název pravidla, Počáteční IP adresu a Koncovou IP adresu. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a upravte ho. Kliknutím na **Uložit** uložte změny.
* Chcete-li odstranit stávající pravidlo, klikněte na tlačítko se třemi tečkami [...] a odstraňte pravidlo kliknutím na tlačítko **Odstranit** . Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další kroky
- Podobně můžete vytvořit skript pro [vytváření a správu pravidel brány firewall Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-firewall-using-cli.md).
- [Vytvořením a správou koncových bodů a pravidel služby Virtual Network a pomocí Azure Portal](howto-manage-vnet-using-portal.md)můžete lépe zabezpečit přístup k vašemu serveru.
- Nápovědu k připojení k serveru Azure Database for PostgreSQL najdete v tématu [knihovny připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).
