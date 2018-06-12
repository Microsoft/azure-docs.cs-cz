---
title: Vytvářet a spravovat pravidla brány firewall MySQL v Azure Database pro databázi MySQL
description: Vytvářet a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí portálu Azure
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f7d2d97049d73387f44f55bbd2fb90a6174a9df2
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265856"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Vytvářet a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí portálu Azure
Pravidla brány firewall na úrovni serveru umožňují správcům přístup k databázi Azure pro Server databáze MySQL z zadaná IP adresa nebo rozsah IP adres. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

1. Na stránce server MySQL, v části nastavení klikněte na **zabezpečení připojení** chcete otevřít stránku zabezpečení připojení pro databázi Azure pro databázi MySQL.

   ![Portál Azure – klikněte na možnost zabezpečení připojení](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klikněte na tlačítko **přidat Moje IP** na panelu nástrojů. Toto automaticky vytvoří pravidlo brány firewall s veřejnou IP adresu počítače, kterou posuzuje systému Azure.

   ![Portál Azure – klikněte na tlačítko Přidat Moje IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Před uložením konfigurace ověřte IP adresu. V některých situacích IP adresu zjištěnými pomocí portálu Azure se liší od adresa IP použitá při přístupu k Internetu a servery Azure. Proto musíte změnit počáteční IP a koncová IP adresa, aby funkce pravidla podle očekávání.

   Zkontrolujte vlastní IP adresu pomocí vyhledávacího webu nebo jiný nástroj, online. Například hledání "co je adresa IP". 

   ![Bing co je můj IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Přidáte další adresní rozsahy. V pravidlech brány firewall pro databázi Azure pro databázi MySQL můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete omezit platnost pravidla pro jednu IP adresu, zadejte stejnou adresu v polích Počáteční IP a koncová IP adresa. Otevření brány firewall umožňuje správci, uživatelů a aplikací pro přístup k jakékoli databázi na serveru databáze MySQL, ke kterým mají platné přihlašovací údaje.

   ![Portál Azure – pravidla brány firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klikněte na tlačítko **Uložit** na panelu nástrojů Uložit toto pravidlo brány firewall na úrovni serveru. Počkejte, než pro potvrzení, že aktualizace, která se pravidla brány firewall byla úspěšně dokončena.

   ![Portál Azure – klikněte na tlačítko Uložit](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Povolit aplikacím z Azure se připojit k vaší databázi Azure pro server databáze MySQL, musí být povoleno Azure připojení. Například k hostování aplikace Azure Web Apps nebo aplikaci, která běží ve virtuálním počítači Azure nebo pro připojení z brány správy dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall povolit těchto připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** možnost k **ON** na portálu ze **zabezpečení připojení** podokně a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolena, požadavek nebylo dosaženo databáze Azure pro server databáze MySQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Spravovat existující pravidla brány firewall na úrovni serveru pomocí portálu Azure
Opakujte kroky ke správě pravidla brány firewall.
* Chcete-li přidat počítač, klikněte na tlačítko **+ přidat Moje IP**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete přidat další IP adresy, zadejte **název pravidla**, **počáteční IP**, a **KONCOVÁ IP adresa**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit existující pravidlo, klikněte na pole v pravidle a potom upravte. Kliknutím na **Uložit** uložte změny.
* Pokud chcete odstranit existující pravidlo, klikněte na tlačítko se třemi tečkami [...] a pak klikněte na tlačítko **odstranit**. Kliknutím na **Uložit** uložte změny.


## <a name="next-steps"></a>Další postup
- Podobně můžete skript pro [vytvořit a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md).
- Pomoc při připojování k databázi Azure pro server databáze MySQL, najdete v tématu [knihovny připojení pro databázi Azure pro databázi MySQL](./concepts-connection-libraries.md)
