---
title: Správa pravidel brány firewall – portál Azure – Databáze Azure pro PostgreSQL – jeden server
description: Vytvoření a správa pravidel brány firewall pro Azure Database for PostgreSQL – jeden server pomocí portálu Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770301"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Vytvoření a správa pravidel brány firewall pro Azure Database for PostgreSQL – jeden server pomocí portálu Azure
Pravidla brány firewall na úrovni serveru lze použít ke správě přístupu k databázi Azure pro PostgreSQL Server ze zadané IP adresy nebo rozsahu IP adres.

Pravidla virtuální sítě (VNet) lze také použít k zabezpečení přístupu k serveru. Přečtěte si další informace o [vytváření a správě koncových bodů a pravidel služby Virtuální síť pomocí portálu Azure](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- Server [Vytvoření databáze Azure pro PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal
1. Na stránce PostgreSQL server v části Nastavení klikněte na **zabezpečení připojení** a otevřete stránku zabezpečení připojení pro Azure Database for PostgreSQL.

   ![Portál Azure – klikněte na Zabezpečení připojení.](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Na panelu nástrojů klikněte na **Přidat moji IP** adresu. Tím se automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak ji vnímá systém Azure.

   ![Portál Azure – klikněte na Přidat moji IP adresu.](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Před uložením konfigurace ověřte svou IP adresu. V některých situacích se IP adresa pozorovaná portálem Azure portal liší od IP adresy používané při přístupu k internetu a serverům Azure. Proto může být nutné změnit počáteční IP a koncovou IP adresu, aby pravidlo fungovalo podle očekávání.
   Pomocí vyhledávače nebo jiného online nástroje zkontrolujte svou vlastní IP adresu. Vyhledejte například "jaká je moje IP adresa".

   ![Bing hledat Co je moje IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Přidejte další rozsahy adres. V pravidlech brány firewall pro Databázi Azure pro PostgreSQL můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete omezit pravidlo na jednu adresu IP, zadejte stejnou adresu do pole Pro Počáteční IP a End IP. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k libovolné databázi na serveru PostgreSQL, ke které mají platná pověření.

   ![Azure Portal – pravidla brány firewall](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klepnutím na **tlačítko Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Počkejte na potvrzení, že aktualizace pravidel brány firewall byla úspěšná.

   ![Portál Azure – klikněte na Uložit.](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Aby se aplikace z Azure mohly připojit k vašemu serveru Azure Database for PostgreSQL, musí být povolená připojení Azure. Například k hostování aplikace Azure Web Apps nebo aplikace, která běží ve virtuálním počítači Azure, nebo připojení z brány pro správu dat Azure Data Factory. Prostředky nemusí být ve stejné virtuální síti (VNet) nebo skupiny prostředků pro pravidlo brány firewall povolit tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které umožňují tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Případně můžete nastavit **možnost Povolit přístup ke službám Azure** na **zapnuto na** portálu z podokna zabezpečení **Připojení** a klikněte na **Uložit**. Pokud není povolen pokus o připojení, požadavek nedosáhne databáze Azure pro postgresql server.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Správa stávajících pravidel brány firewall na úrovni serveru na webu Azure Portal
Opakujte postup pro správu pravidel brány firewall.
* Chcete-li přidat aktuální počítač, klepněte na tlačítko + **Přidat adresu IP**. Kliknutím na **Uložit** uložte změny.
* Pro přidání dalších IP adres zadejte Název pravidla, Počáteční IP adresu a Koncovou IP adresu. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a upravte ho. Kliknutím na **Uložit** uložte změny.
* Chcete-li odstranit existující pravidlo, klepněte na tři tečky [...] a klepnutím na tlačítko **Odstranit** pravidlo odeberte. Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další kroky
- Podobně můžete skriptovat [k vytvoření a správě azure database pro pravidla brány firewall PostgreSQL pomocí Azure CLI](howto-manage-firewall-using-cli.md).
- Další zabezpečení přístupu k serveru [vytvořením a správou koncových bodů a pravidel služby Virtuální síť pomocí portálu Azure](howto-manage-vnet-using-portal.md).
- Nápovědu k připojení k serveru Azure Database for PostgreSQL najdete v [tématu Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md).
