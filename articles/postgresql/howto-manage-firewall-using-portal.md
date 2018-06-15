---
title: Vytváření a Správa pravidel brány firewall v databázi Azure pro PostgreSQL
description: Vytvářet a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí portálu Azure
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bef927cff49d957728a2a12362786d48d60e61b7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29690335"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Vytvářet a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí portálu Azure
Pravidla brány firewall na úrovni serveru umožňují správcům přístup k databázi Azure pro PostgreSQL Server z zadaná IP adresa nebo rozsah IP adres. 

## <a name="prerequisites"></a>Požadavky
Chcete-li krok tímto průvodcem postupy, je třeba:
- Server [vytvoření databáze Azure pro PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal
1. Na stránce server PostgreSQL, v části nastavení klikněte na **zabezpečení připojení** chcete otevřít stránku zabezpečení připojení pro databázi Azure pro PostgreSQL.

  ![Portál Azure – klikněte na možnost zabezpečení připojení](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klikněte na tlačítko **přidat Moje IP** na panelu nástrojů. Toto automaticky vytvoří pravidlo brány firewall s veřejnou IP adresu počítače, kterou posuzuje systému Azure.

  ![Portál Azure – klikněte na tlačítko Přidat Moje IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Před uložením konfigurace ověřte IP adresu. V některých situacích IP adresu zjištěnými pomocí portálu Azure se liší od adresa IP použitá při přístupu k Internetu a servery Azure. Proto musíte změnit počáteční IP a koncová IP adresa, aby funkce pravidla podle očekávání.
Zkontrolujte vlastní IP adresu pomocí vyhledávacího webu nebo jiný nástroj, online. Například vyhledejte "Jaký je můj IP."

  ![Co je můj IP Bing hledání](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Přidáte další adresní rozsahy. V pravidlech brány firewall pro databázi Azure pro PostgreSQL můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete omezit platnost pravidla pro jednu IP adresu, zadejte stejnou adresu v poli pro počáteční IP a koncová IP adresa. Otevření brány firewall umožňuje správci, uživatelů a aplikací pro přihlášení k jakékoli databázi na serveru PostgreSQL, k němuž mají platné přihlašovací údaje.

  ![Portál Azure – pravidla brány firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klikněte na tlačítko **Uložit** na panelu nástrojů Uložit toto pravidlo brány firewall na úrovni serveru. Počkejte, než pro potvrzení, že aktualizace, která se pravidla brány firewall bylo úspěšné.

  ![Portál Azure – klikněte na tlačítko Uložit](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Povolit aplikacím z Azure se připojit k vaší databázi Azure pro PostgreSQL server, musí být povoleno Azure připojení. Například k hostování aplikace Azure Web Apps nebo aplikaci, která běží ve virtuálním počítači Azure nebo pro připojení z brány správy dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall povolit těchto připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** možnost k **ON** na portálu ze **zabezpečení připojení** podokně a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolena, požadavek nebylo dosaženo databáze Azure pro PostgreSQL server.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Správa stávajících pravidel brány firewall na úrovni serveru na webu Azure Portal
Opakujte kroky ke správě pravidla brány firewall.
* Pokud chcete přidat počítač, kliknutím na tlačítko + **přidat Moje IP**. Kliknutím na **Uložit** uložte změny.
* Pro přidání dalších IP adres zadejte Název pravidla, Počáteční IP adresu a Koncovou IP adresu. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a upravte ho. Kliknutím na **Uložit** uložte změny.
* Pokud chcete odstranit existující pravidlo, klikněte na tlačítko se třemi tečkami [...] a klikněte na tlačítko **odstranit** pro odebrání pravidla. Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další postup
- Podobně můžete skript pro [vytvořit a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md).
- Pomoc při připojování k databázi Azure pro PostgreSQL server najdete v tématu [knihovny připojení pro databázi Azure pro PostgreSQL](concepts-connection-libraries.md).
