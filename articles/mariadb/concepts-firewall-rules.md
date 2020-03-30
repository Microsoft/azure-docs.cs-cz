---
title: Pravidla brány firewall – databáze Azure pro MariaDB
description: Další informace o použití pravidel brány firewall k povolení připojení k serveru Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 743e3f50d747993250399493d97fc2becab19319
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532038"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Pravidla brány firewall serveru Azure Database for MariaDB
Brány firewall znemožňují veškerý přístup k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě původní adresy IP každého požadavku.

Chcete-li nakonfigurovat bránu firewall, vytvořte pravidla brány firewall, která určují rozsahy přijatelných adres IP. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celé databázi Azure pro server MariaDB, to znamená, že všechny databáze v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru lze nakonfigurovat pomocí portálu Azure nebo příkazů Azure CLI. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Veškerý přístup k databázi k serveru Azure Database for MariaDB je ve výchozím nastavení blokován bránou firewall. Chcete-li začít používat server z jiného počítače, je třeba zadat jedno nebo více pravidel brány firewall na úrovni serveru, aby byl povolen přístup k serveru. Pomocí pravidel brány firewall určete, které adresy IP se pohybují od Internetu, aby je bylo možné povolit. Samotná pravidla brány firewall nemají vliv na přístup k samotnému webu portálu Azure.

Pokusy o připojení z Internetu a Azure musí nejprve projít bránou firewall, než se dostanou do databáze Azure pro MariaDB, jak je znázorněno na následujícím diagramu:

![Příklad toku fungování brány firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru platí pro všechny databáze na serveru Azure Database for MariaDB.

Pokud je adresa IP požadavku v rámci jednoho z rozsahů uvedených v pravidlech brány firewall na úrovni serveru, je připojení uděleno.

Pokud je adresa IP požadavku mimo rozsahy zadané v libovolném pravidle brány firewall na úrovni databáze nebo serveru, požadavek na připojení se nezdaří.

## <a name="connecting-from-azure"></a>Připojení z Azure
Doporučujeme najít odchozí IP adresu libovolné aplikace nebo služby a explicitně povolit přístup k těmto jednotlivým IP adresám nebo rozsahům. Můžete například najít odchozí IP adresu služby Azure App Service nebo použít veřejnou IP adresu vázanou na virtuální počítač nebo jiný prostředek (viz níže informace o připojení k privátním ip adresám virtuálního počítače přes koncové body služby). 

Pokud pro vaši službu Azure není dostupná pevná odchozí IP adresa, můžete zvážit povolení připojení ze všech IP adres datového centra Azure. Toto nastavení lze povolit z portálu Azure nastavením **možnosti Povolit přístup ke službám Azure** **na ZAPNUTO** v podokně **zabezpečení Připojení** a stisknutím **klávesy Uložit**. Z azure CLI, nastavení pravidla brány firewall s počáteční a koncovou adresu rovnou 0.0.0.0 dělá ekvivalent. Pokud není povolen pokus o připojení, požadavek nedosáhne databáze Azure pro server MariaDB.

> [!IMPORTANT]
> Možnost **Povolit přístup ke službám Azure** nakonfiguruje bránu firewall tak, aby umožňovala všechna připojení z Azure včetně připojení z předplatných jiných zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Konfigurace povolení přístupu ke službám Azure na portálu](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Připojení z virtuální sítě
Pokud se chcete bezpečně připojit k databázi Azure pro server MariaDB z virtuální sítě, zvažte použití [koncových bodů služby Virtuální sítě](./concepts-data-access-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě portálu Azure lze pravidla brány firewall spravovat programově pomocí nastavení cli Azure. 

Viz taky [Vytvoření a správa pravidel brány firewall Azure Database for MariaDB pomocí azure CLI](./howto-manage-firewall-cli.md).

## <a name="troubleshooting-firewall-issues"></a>Poradce při potížích s bránou firewall
Zvažte následující body, pokud se přístup k databázi Microsoft Azure pro serverovou službu MariaDB nechová očekávaným způsobem:

* **Změny seznamu povolených položek se dosud neprojevily:** Může být stejně jako pět minut zpoždění pro změny v databázi Azure pro mariadb server konfigurace brány firewall se projeví.

* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění na azure databázi pro mariadb server nebo použité heslo je nesprávné, připojení k databázi Azure pro server MariaDB je odepřen. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje.

* **Dynamická IP adresa:** Pokud máte připojení k Internetu s dynamickým adresováním IP adresování a máte potíže s průchodem bránou firewall, můžete zkusit jedno z následujících řešení:

   * Požádejte svého poskytovatele internetových služeb (ISP) o rozsah IP adres přiřazený vašim klientským počítačům, které přistupují k databázi Azure pro server MariaDB, a pak přidejte rozsah IP adres jako pravidlo brány firewall.

   * Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

* **Ip adresa serveru se zdá být veřejná:** Připojení k azure databázi pro server MariaDB jsou směrovány přes veřejně přístupné brány Azure. Skutečná IP adresa serveru je však chráněná bránou firewall. Další informace naleznete v [článku architektury připojení](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Další kroky
- [Vytváření a správa pravidel brány firewall Azure Database for MariaDB pomocí portálu Azure](./howto-manage-firewall-portal.md)
- [Vytvoření a správa pravidel brány firewall Azure Database for MariaDB pomocí azure cli](./howto-manage-firewall-cli.md)
- [Koncové body služby Virtuální sítě v databázi Azure pro MariaDB](./concepts-data-access-security-vnet.md)
