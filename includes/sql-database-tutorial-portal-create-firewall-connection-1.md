---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008521"
---
## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL

Azure SQL database existuje v rámci definovanou sadu [výpočetních a úložných kapacit](../articles/sql-database/sql-database-service-tiers-dtu.md). Databáze funguje v rámci [skupiny prostředků Azure](../articles/azure-resource-manager/resource-group-overview.md) a [logického serveru Azure SQL database](../articles/sql-database/sql-database-features.md).

Pomocí následujících kroků vytvořte prázdnou databázi SQL.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

1. Na **nový** stránce **databází** > **SQL Database**.

   ![Vytvoření prázdné databáze](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. V **SQL Database** podokně, zadejte nebo vyberte následující hodnoty:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název databáze** | *yourDatabase* | Platné názvy databází najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
   | **Předplatné** | *yourSubscription*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | *yourResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
   | **Výběr zdroje** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

   ![vytvoření databáze](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Vyberte **Server** konfigurace serveru pro vaši novou databázi. Potom zadejte nebo vyberte následující hodnoty:

      | Nastavení       | Navrhovaná hodnota | Popis |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
      | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers).|
      | **Heslo** | Libovolné platné heslo | Heslo musí mít aspoň 8 znaků a musí používat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
      | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/). |

      Zvolte **Vybrat**.

      ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Vyberte **cenová úroveň** určit úroveň služby, počet Dtu a velikost úložiště. Prozkoumejte možnosti Dtu a úložiště, které je k dispozici na jednotlivých úrovních služby.

      Po výběru na úrovni serveru, počet Dtu a velikost úložiště, vyberte **použít**.

   1. Zadejte **kolace** pro prázdnou databázi (pro účely tohoto kurzu použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [kolace](/sql/t-sql/statements/collations).

1. Teď, když jste dokončili **SQL Database** formuláře, vyberte **vytvořit** k vytvoření databáze. Tento krok může trvat až minutu a půl dokončete.

1. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

     ![oznámení](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Vytvoření pravidla brány firewall

Službu SQL database vytvoří bránu firewall na úrovni serveru, aby se zabránilo externím aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru. Postupujte podle těchto kroků můžete vytvořit [pravidlo brány firewall na úrovni serveru SQL database](../articles/sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta. Tento proces umožní externí připojení přes bránu firewall SQL database pro vaši IP adresu.

> [!NOTE]
> SQL database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k serveru Azure SQL database, dokud správce otevře port 1433.

1. Jakmile se nasazení dokončí, zvolte **databází SQL** z nabídky na levé straně a pak vyberte *yourDatabase* na **databází SQL** stránky. **Přehled** stránce pro vaši databázi otevře, se zobrazí plně kvalifikovaný název (například *yourserver.database.windows.net*) a poskytne vám možnosti další konfigurace.

1. Zkopírujte plně kvalifikovaný název serveru použít pro připojení k serveru a jeho databázím v následujících krocích.

   ![název serveru](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Vyberte **nastavit bránu firewall serveru** na panelu nástrojů. **Nastavení brány Firewall** otevře se stránka pro server služby SQL database.

   ![pravidlo brány firewall serveru](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Vyberte **přidat IP adresu klienta** na panelu nástrojů přidat vaši aktuální IP adresu na nové pravidlo brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

   1. Zvolte **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

   1. Vyberte **OK** a ukončete **nastavení brány Firewall** stránky.

Vaše IP adresa mohou procházet přes bránu firewall a nyní se můžete připojit k serveru SQL database a jeho databázím pomocí aplikace SSMS nebo jiného nástroje podle vašeho výběru. Nezapomeňte použít účet správce serveru, kterou jste vytvořili dříve.

> [!IMPORTANT]
> Přístup přes bránu firewall služby SQL database je ve výchozím nastavení povolené pro všechny služby Azure. Vyberte **OFF** na této stránce provedete zákaz pro všechny služby Azure.
