---
title: Pravidla brány firewall – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak používat pravidla brány firewall pro připojení k Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157266"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Pravidla brány firewall v databázi Azure pro PostgreSQL – jeden server
Brána firewall serveru Azure Database for PostgreSQL zabrání veškerému přístupu k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě původní adresy IP každého požadavku.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celé databázi Azure pro PostgreSQL Server, to znamená, že všechny databáze v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru lze nakonfigurovat pomocí portálu Azure nebo pomocí příkazů Azure CLI. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Veškerý přístup k databázi k serveru Azure Database for PostgreSQL je ve výchozím nastavení blokován bránou firewall. Chcete-li začít používat server z jiného počítače, je třeba zadat jedno nebo více pravidel brány firewall na úrovni serveru, aby byl povolen přístup k serveru. Pomocí pravidel brány firewall určete, které adresy IP se pohybují od Internetu, aby je bylo možné povolit. Samotná pravidla brány firewall nemají vliv na přístup k samotnému webu portálu Azure.
Pokusy o připojení z Internetu a Azure musí nejprve projít bránou firewall, než se dostanou do vaší databáze PostgreSQL, jak je znázorněno na následujícím diagramu:

![Příklad toku fungování brány firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru platí pro všechny databáze na stejném serveru Azure Database for PostgreSQL. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno.
Pokud adresa IP požadavku není v rozsahu zadaném v žádném z pravidel brány firewall na úrovni serveru, požadavek na připojení se nezdaří.
Například pokud se vaše aplikace připojí k ovladači JDBC pro PostgreSQL, může dojít k této chybě při pokusu o připojení, když brána firewall blokuje připojení.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Připojení z Azure
Doporučujeme najít odchozí IP adresu libovolné aplikace nebo služby a explicitně povolit přístup k těmto jednotlivým IP adresám nebo rozsahům. Můžete například najít odchozí IP adresu služby Azure App Service nebo použít veřejnou IP adresu vázanou na virtuální počítač nebo jiný prostředek (viz níže informace o připojení k privátním ip adresám virtuálního počítače přes koncové body služby). 

Pokud pro vaši službu Azure není dostupná pevná odchozí IP adresa, můžete zvážit povolení připojení ze všech IP adres datového centra Azure. Toto nastavení lze povolit z portálu Azure nastavením **možnosti Povolit přístup ke službám Azure** **na ZAPNUTO** v podokně **zabezpečení Připojení** a stisknutím **klávesy Uložit**. Z azure CLI, nastavení pravidla brány firewall s počáteční a koncovou adresu rovnou 0.0.0.0 dělá ekvivalent. Pokud není povolen pokus o připojení, požadavek nedosáhne databáze Azure pro postgresql server.

> [!IMPORTANT]
> Možnost **Povolit přístup ke službám Azure** nakonfiguruje bránu firewall tak, aby umožňovala všechna připojení z Azure včetně připojení z předplatných jiných zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Konfigurace povolení přístupu ke službám Azure na portálu](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Připojení z virtuální sítě
Pokud se chcete bezpečně připojit k azure databázi pro PostgreSQL server z virtuální sítě, zvažte použití [koncových bodů služby Virtuální sítě](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě portálu Azure lze pravidla brány firewall spravovat programově pomocí azure cli.
Viz taky [Vytvoření a správa pravidel brány Firewall Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Poradce při potížích s bránou firewall
Zvažte následující body, když se přístup ke službě Microsoft Azure Database for PostgreSQL Server nechová tak, jak očekáváte:

* **Změny seznamu povolených položek se dosud neprojevily:** Může být stejně jako pět minut zpoždění pro změny v azure databáze pro postgreSQL server konfigurace brány firewall se projeví.

* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění k azure databázi pro postgreSQL server nebo použité heslo je nesprávné, připojení k Azure Database pro PostgreSQL server je odepřen. Vytvoření nastavení brány firewall poskytuje klientům pouze možnost pokusit se připojit k serveru. každý klient musí stále poskytnout potřebná pověření zabezpečení.

   Například pomocí klienta JDBC se může zobrazit následující chyba.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: ověřování hesla se nezdařilo pro uživatele "yourusername"

* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

   * Požádejte svého poskytovatele internetových služeb (ISP) o rozsah IP adres přiřazený vašim klientským počítačům, které přistupují k databázi Azure pro PostgreSQL Server, a pak přidejte rozsah IP adres jako pravidlo brány firewall.

   * Místo toho získejte statické adresování IP pro klientské počítače a přidejte statickou adresu IP jako pravidlo brány firewall.

* **Ip adresa serveru se zdá být veřejná:** Připojení k Azure Database for PostgreSQL server jsou směrovány přes veřejně přístupné brány Azure. Skutečná IP adresa serveru je však chráněná bránou firewall. Další informace naleznete v [článku architektury připojení](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Další kroky
Články o vytváření pravidel brány firewall na úrovni serveru a databáze naleznete v tématu:
* [Vytváření a správa pravidel brány firewall Azure Database for PostgreSQL pomocí portálu Azure](howto-manage-firewall-using-portal.md)
* [Vytváření a správa pravidel brány Firewall Azure Database for PostgreSQL pomocí azure CLI](howto-manage-firewall-using-cli.md)
- [Koncové body služby Virtuální sítě v databázi Azure pro PostgreSQL](./concepts-data-access-and-security-vnet.md)
