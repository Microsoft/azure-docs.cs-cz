---
title: Vytvoření a Správa pravidel brány firewall pro MySQL ve službě Azure Database for MySQL
description: Vytvoření a správě Azure Database for MySQL – pravidla brány firewall pomocí webu Azure portal
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 598af6bb945318f0a76ffe094dd5786abacccc3f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543525"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Vytvoření a správě Azure Database for MySQL pravidla brány firewall pomocí webu Azure portal
Pravidla brány firewall na úrovni serveru umožňují správcům přístup k Azure Database for MySQL Server ze zadané IP adresy nebo rozsah IP adres. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

1. Na stránce v části Nastavení serveru MySQL záhlaví, klikněte na tlačítko **zabezpečení připojení** otevřete stránku zabezpečení připojení pro službu Azure Database for MySQL.

   ![Portál Azure – zabezpečení připojení klikněte na](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klikněte na tlačítko **přidat moji IP adresu** na panelu nástrojů. Tím automaticky se vytvoří pravidlo brány firewall s veřejnou IP adresu počítače, vnímanou systému Azure.

   ![Azure portal – klikněte na tlačítko Přidat Moje IP adresa](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Před uložením konfiguraci ověřte vaši IP adresu. V některých případech IP adresu prostřednictvím webu Azure portal se liší od IP adresu používat při přístupu k Internetu a servery Azure. Proto budete muset změnit počáteční IP adresa a koncová IP adresa, aby pravidlo fungují podle očekávání.

   Pomocí vyhledávacího webu nebo jiný nástroj pro online zkontrolujte svou vlastní IP adresu. Například hledání "jaká je Moje IP adresa". 

   ![Bing pro co je Moje IP adresa](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Přidejte další adresní rozsahy adres. V pravidlech brány firewall pro službu Azure Database for MySQL můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete omezit platnost pravidla pro jednu IP adresu, zadejte stejnou adresu do pole Počáteční IP adresa a koncová IP adresa. Otevření brány firewall umožňuje správci, uživatelé a aplikace pro přístup k libovolné databázi na serveru MySQL, ke kterému mají platné přihlašovací údaje.

   ![Azure portal – pravidla brány firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klikněte na tlačítko **Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Čekání na potvrzení, jestli aktualizace, která se pravidla brány firewall se úspěšně dokončila.

   ![Azure portal – klikněte na možnost Uložit](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete umožnit aplikacím z Azure připojení k Azure Database for MySQL server, musí být povolená připojení Azure. Například pro hostování aplikace Azure Web Apps nebo aplikace, která běží ve Virtuálním počítači Azure nebo k připojení ze Brána pro správu dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall, aby tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** umožňuje **ON** portálu ze **zabezpečení připojení** podokna a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolený, požadavek vůbec nedostane serveru Azure Database for MySQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Správa stávajících pravidel brány firewall na úrovni serveru pomocí webu Azure portal
Opakováním těchto kroků můžete spravovat pravidla brány firewall.
* Chcete-li přidat aktuální počítač, klikněte na tlačítko **+ přidat moji IP adresu**. Kliknutím na **Uložit** uložte změny.
* Chcete-li přidat další IP adresy, zadejte **název pravidla**, **počáteční IP adresa**, a **KONCOVÁ IP adresa**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a potom upravte. Kliknutím na **Uložit** uložte změny.
* Pokud chcete odstranit stávající pravidlo, klikněte na tlačítko se třemi tečkami [...] a pak klikněte na **odstranit**. Kliknutím na **Uložit** uložte změny.


## <a name="next-steps"></a>Další postup
- Podobně můžete používat skripty pro [vytvořit a spravovat Azure Database for MySQL pravidel brány firewall pomocí Azure CLI](howto-manage-firewall-using-cli.md).
- Pomoc při připojování k serveru Azure Database for MySQL, naleznete v tématu [připojení knihoven pro službu Azure Database for MySQL](./concepts-connection-libraries.md)
