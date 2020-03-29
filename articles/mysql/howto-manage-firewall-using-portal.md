---
title: Správa pravidel brány firewall – portál Azure – Azure Database for MySQL
description: Vytváření a správa pravidel brány firewall Azure Database for MySQL pomocí portálu Azure
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: edd6403ed3d7607eb96bc7c6a603c3fef8a4f99e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063552"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Vytváření a správa pravidel brány firewall Azure Database for MySQL pomocí portálu Azure
Pravidla brány firewall na úrovni serveru lze použít ke správě přístupu k databázi Azure pro MySQL Server ze zadané IP adresy nebo rozsahu IP adres. 

Pravidla virtuální sítě (VNet) lze také použít k zabezpečení přístupu k serveru. Přečtěte si další informace o [vytváření a správě koncových bodů a pravidel služby Virtuální síť pomocí portálu Azure](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

1. Na stránce MySQL server v části Nastavení klikněte na **Zabezpečení připojení** a otevřete stránku Zabezpečení připojení pro Databázi Azure pro MySQL.

   ![Portál Azure – klikněte na zabezpečení připojení.](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Na panelu nástrojů klikněte na **Přidat moji IP** adresu. Tím se automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak ji vnímá systém Azure.

   ![Portál Azure – klikněte na Přidat moji IP adresu.](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Před uložením konfigurace ověřte svou IP adresu. V některých situacích se IP adresa pozorovaná portálem Azure portal liší od IP adresy používané při přístupu k internetu a serverům Azure. Proto může být nutné změnit počáteční IP a koncovou IP adresu, aby pravidlo fungovalo podle očekávání.

   Pomocí vyhledávače nebo jiného online nástroje zkontrolujte svou vlastní IP adresu. Například vyhledejte "jaká je moje IP adresa".

4. Přidejte další rozsahy adres. V pravidlech brány firewall pro Azure Database for MySQL můžete zadat jednu IP adresu nebo rozsah adres. Chcete-li omezit pravidlo na jednu adresu IP, zadejte stejnou adresu do polí Počáteční IP a End IP. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k libovolné databázi na serveru MySQL, ke které mají platná pověření.

   ![Azure Portal – pravidla brány firewall](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klepnutím na **tlačítko Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Počkejte na potvrzení, že aktualizace pravidel brány firewall je úspěšná.

   ![Portál Azure – klikněte na Uložit.](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Aby se aplikace z Azure mohly připojit k vašemu serveru Azure Database for MySQL, musí být povolená připojení Azure. Například k hostování aplikace Azure Web Apps nebo aplikace, která běží ve virtuálním počítači Azure, nebo připojení z brány pro správu dat Azure Data Factory. Prostředky nemusí být ve stejné virtuální síti (VNet) nebo skupiny prostředků pro pravidlo brány firewall povolit tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které umožňují tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Případně můžete nastavit **možnost Povolit přístup ke službám Azure** na **zapnuto na** portálu z podokna zabezpečení **Připojení** a klikněte na **Uložit**. Pokud není povolen pokus o připojení, požadavek nedosáhne databáze Azure pro server MySQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Správa stávajících pravidel brány firewall na úrovni serveru pomocí portálu Azure
Opakujte postup pro správu pravidel brány firewall.
* Chcete-li přidat aktuální počítač, klepněte na tlačítko **+ Přidat adresu IP**. Kliknutím na **Uložit** uložte změny.
* Chcete-li přidat další adresy IP, zadejte **název pravidla**, POČÁTEČNÍ **ADRESU A**ADRESU **END**. Kliknutím na **Uložit** uložte změny.
* Chcete-li upravit existující pravidlo, klepněte na libovolné pole v pravidle a potom upravte. Kliknutím na **Uložit** uložte změny.
* Chcete-li odstranit existující pravidlo, klepněte na tři tečky [...], a potom klepněte na tlačítko **Odstranit**. Kliknutím na **Uložit** uložte změny.


## <a name="next-steps"></a>Další kroky
     Similarly, you can script to [Create and manage Azure Database for MySQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md).
     Further secure access to your server by [creating and managing Virtual Network service endpoints and rules using the Azure portal](howto-manage-vnet-using-portal.md).
        For help in connecting to an Azure     atabase for MySQL server, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        