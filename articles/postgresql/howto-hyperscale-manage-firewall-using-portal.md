---
title: Správa pravidel brány firewall – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Vytvoření a správa pravidel brány firewall pro Azure Database for PostgreSQL – Hyperscale (Citus) pomocí portálu Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977535"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Správa pravidel brány firewall pro Azure Database for PostgreSQL – Hyperscale (Citus)
Pravidla brány firewall na úrovni serveru lze použít ke správě přístupu k uzlu koordinátora Hyperscale (Citus) ze zadané adresy IP nebo rozsahu adres IP.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- Skupina serverů [Vytvořte databázi Azure pro skupinu serverů PostgreSQL – Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

> [!NOTE]
> Tato nastavení jsou také přístupné při vytváření skupiny serverů Azure Database for PostgreSQL – Hyperscale (Citus). Na kartě **Síť** klikněte na **veřejný koncový bod**.
> ![Azure portal – karta síť](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Na stránce skupiny serverů PostgreSQL pod záhlavím Zabezpečení otevřete pravidla brány firewall klepnutím na **položku Síť.**

   ![Portál Azure – klikněte na Síťové a sociální sítě.](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Klepněte na tlačítko **Přidat IP klienta**, buď na panelu nástrojů (možnost A níže), nebo v odkazu (možnost B). V obou směrech automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak ji vnímá systém Azure.

   ![Portál Azure – klikněte na Přidat IP adresu klienta.](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Případně kliknutím **na +Přidat 0.0.0.0 - 255.255.255.255** (vpravo od možnosti B) umožňuje nejen vaši IP adresu, ale celý internet pro přístup k portu uzlu koordinátora 5432. V takovém případě klienti stále musí přihlásit se správným uživatelským jménem a heslem pro použití clusteru. Přesto doporučujeme povolit celosvětový přístup pouze na krátkou dobu a pouze pro neprodukční databáze.

3. Před uložením konfigurace ověřte svou IP adresu. V některých situacích se IP adresa pozorovaná portálem Azure portal liší od IP adresy používané při přístupu k internetu a serverům Azure. Proto může být nutné změnit počáteční IP a koncovou IP adresu, aby pravidlo fungovalo podle očekávání.
   Pomocí vyhledávače nebo jiného online nástroje zkontrolujte svou vlastní IP adresu. Vyhledejte například "jaká je moje IP adresa".

   ![Bing hledat Co je moje IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Přidejte další rozsahy adres. V pravidlech brány firewall můžete zadat jednu adresu IP nebo rozsah adres. Pokud chcete omezit pravidlo na jednu adresu IP, zadejte stejnou adresu do pole Pro Počáteční IP a End IP. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k uzlu koordinátora na portu 5432.

5. Klepnutím na **tlačítko Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Počkejte na potvrzení, že aktualizace pravidel brány firewall byla úspěšná.

## <a name="connecting-from-azure"></a>Připojení z Azure

Existuje snadný způsob, jak udělit hyperškálování přístup k databázi Hyperscale aplikacím hostovaným v Azure (jako je například aplikace Azure Web Apps nebo aplikace spuštěné v virtuálním počítači Azure). Jednoduše nastavte možnost **Povolit službám a prostředkům Azure pro přístup k této skupině serverů** na **Ano** na portálu z podokna **Sítě** a klikněte na **Uložit**.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Správa stávajících pravidel brány firewall na úrovni serveru na webu Azure Portal
Opakujte postup pro správu pravidel brány firewall.
* Chcete-li přidat aktuální počítač, klepněte na tlačítko + **Přidat IP klienta**. Kliknutím na **Uložit** uložte změny.
* Pro přidání dalších IP adres zadejte Název pravidla, Počáteční IP adresu a Koncovou IP adresu. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a upravte ho. Kliknutím na **Uložit** uložte změny.
* Chcete-li odstranit existující pravidlo, klepněte na tři tečky [...] a klepnutím na tlačítko **Odstranit** pravidlo odeberte. Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [konceptu pravidel brány firewall](concepts-hyperscale-firewall-rules.md), včetně řešení problémů s připojením.
