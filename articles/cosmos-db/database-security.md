---
title: Databáze zabezpečení – Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak Azure Cosmos DB poskytuje databáze ochrany a data zabezpečení pro vaše data.
keywords: databáze nosql zabezpečení, informace o zabezpečení, zabezpečení dat, šifrování databáze, ochrana databáze, zásady zabezpečení, zabezpečení testování
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: aa04ae8d5bdccb52e3f63fb2dfb3c75df83b7a54
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611615"
---
# <a name="azure-cosmos-db-database-security"></a>Zabezpečení databáze Azure Cosmos DB

Tento článek popisuje osvědčené postupy zabezpečení databáze a klíčové funkce, které nabízí Azure DB Cosmos můžete zabránit, zjišťování a reakce na porušení databáze.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Co je nového v Azure Cosmos DB zabezpečení?

Šifrování v klidovém stavu je nyní k dispozici pro dokumenty a záloh uložených v Azure DB Cosmos ve všech oblastech Azure. Šifrování v klidovém stavu se automaticky použije nové i stávající zákazníků v těchto oblastech. Není nutné konfigurovat nic; a získáte stejnou skvělé latenci, propustnost, dostupnosti a funkce jako před s výhodou znalost vašich dat je bezpečné a zabezpečení pomocí šifrování v klidovém stavu.

## <a name="how-do-i-secure-my-database"></a>Jak zabezpečit databáze? 

Zabezpečení dat je sdílený odpovědnost mezi vy, zákazníků a váš poskytovatel databáze. V závislosti na poskytovateli databáze, který zvolíte se může lišit množství odpovědnosti, kterou jste provádění. Pokud si zvolíte v případě místních řešení, potřebujete poskytovat všechno z ochrany koncový bod k fyzickému zabezpečení hardwaru – tzn. bez snadno úlohy. Pokud si zvolíte poskytovatele databáze PaaS cloudu například Azure Cosmos DB, zmenší se výrazně vaší oblasti zájmu. Na následujícím obrázku, vždy pouze vypůjčí na společnosti Microsoft [sdílené odpovědnosti pro Cloud Computing](https://aka.ms/sharedresponsibility) dokumentu white paper, ukazuje, jak vaše odpovědnosti snižuje s poskytovatelem PaaS jako Azure Cosmos DB.

![Odpovědnosti zákazníka a databáze zprostředkovatele](./media/database-security/nosql-database-security-responsibilities.png)

Na předchozím obrázku jsou uvedeny souhrnné cloudu součásti, zabezpečení, ale položky, které potřebujete si dělat starosti speciálně pro vaše řešení databáze? A jak můžeme srovnávat řešení navzájem? 

Doporučujeme následující kontrolní seznam požadavků, na kterém má být porovnán databázovými systémy:

- Zabezpečení sítě a nastavení brány firewall
- Ověření uživatele a podrobné uživatelské ovládací prvky
- Možnost replikovat data globálně pro místní selhání
- Schopnost proveďte převzetí služeb při selhání z jednoho datového centra
- Místní data replikace v rámci datového centra
- Zálohování dat
- Obnovení odstraněná data ze zálohy
- Chránit a izolovat citlivá data
- Monitorování útokům.
- Reakce na útoky
- Možnost geograficky ochranná data řídit omezeními zásad správného řízení dat
- Fyzická ochrana servery v centrech chráněných dat.

A i když to nemusí připadat zřejmé, poslední [rozsáhlé databáze narušení](http://thehackernews.com/2017/01/mongodb-database-security.html) připomenout nám jednoduchý, ale kritický význam následující požadavky:
- Opravit servery, které jsou pořád aktuální
- HTTPS šifrováním výchozí/SSL
- Účty pro správu s silná hesla

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak Azure Cosmos DB zabezpečit databáze?

Podívejme se zpět na předchozí seznamu – kolik tyto požadavky na zabezpečení Azure Cosmos DB poskytuje? Každý jeden jedné.

Pojďme proniknout do každé z nich podrobně.

|Požadavek na zabezpečení|Způsob zabezpečení Azure Cosmos DB|
|---|---|---|
|Zabezpečení sítě|Použití IP brány firewall je první úroveň ochrany zabezpečení databáze. Azure Cosmos DB podporuje zásady řízené řízení přístupu na základě IP pro podporu brány firewall pro příchozí. Ovládací prvky přístupu na základě IP jsou podobná pravidla brány firewall používané systémy tradiční databáze, ale stavu rozbalení tak, aby účet Azure Cosmos DB databáze je k dispozici pouze z schválené sadu počítačů nebo cloudových služeb. <br><br>Azure Cosmos DB umožňuje povolit konkrétní IP adresu (168.61.48.0), rozsah adres IP (168.61.48.0/8) a kombinace IP adresy a rozsahy adres. <br><br>Všechny požadavky z počítače mimo tento seznam povolených jsou blokována Azure Cosmos DB. Požadavky z schválené počítače a cloudové služby pak musí dokončit proces ověřování má být poskytnut řízení přístupu k prostředkům.<br><br>Další informace v [podporu brány firewall pro Azure Cosmos DB](firewall-support.md).|
|Autorizace|Azure Cosmos DB používá na základě hodnoty hash message authentication code (HMAC) pro ověřování. <br><br>Každý požadavek je zakódována pomocí účtu tajný klíč a následné kódování base-64 kódovaného hash je odeslána s každé volání Azure Cosmos DB. Pro ověření žádosti, služba Azure Cosmos DB používá správný tajný klíč a vlastnosti ke generování hodnoty hash a potom porovná hodnotu s v požadavku. Pokud tyto dvě hodnoty odpovídají operaci oprávněný úspěšně a zpracování žádosti, jinak dojde selhání autorizace a zamítnutí žádosti.<br><br>Můžete použít buď [hlavní klíč](secure-access-to-data.md#master-keys), nebo [token prostředku](secure-access-to-data.md#resource-tokens) podrobných při přístupu k prostředku, jako je například dokument.<br><br>Další informace v [zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Uživatele a oprávnění|Pomocí [hlavní klíč](#master-key) pro účet, můžete vytvořit uživatele a oprávnění prostředků na databázi. A [token prostředku](#resource-token) souvisí s oprávnění v databázi a určuje, zda má uživatel přístup (pro čtení a zápis, jen pro čtení, nebo žádný přístup) na prostředek aplikace v databázi. Prostředky aplikace obsahují kolekcí, dokumentů, přílohy, uložené procedury, triggery a UDF. Token prostředku se pak používá při ověřování k poskytování nebo odepřít přístup k prostředku.<br><br>Další informace v [zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Integrace služby Active directory (RBAC)| Může také poskytnout přístup k účtu databáze pomocí řízení přístupu (IAM) na portálu Azure, jak je znázorněno na snímku obrazovky pod touto tabulkou. IAM poskytuje řízení přístupu na základě rolí a integruje se službou Active Directory. Můžete použít integrované role nebo vlastní role pro jednotlivce a skupiny, jak je znázorněno na následujícím obrázku.|
|Globální replikace|Azure Cosmos DB nabízí to globální distribuce, které umožňuje provádět replikaci dat do některého datových center Azure na celém světě s klepnutím na tlačítko. Globální replikace umožňuje globálně škálovat a poskytovat přístup s nízkou latencí k datům po celém světě.<br><br>V kontextu zabezpečení globální replikace zajišťuje ochranu dat před selháním místní.<br><br>Další informace v [distribuci dat globálně](distribute-data-globally.md).|
|Místní převzetí služeb při selhání|Pokud mají replikovat data do více než jednoho datového centra, Azure Cosmos DB automatické navyšování vaše operace by měla místního datového centra přechodu do offline režimu. Můžete vytvořit seznam oblastí převzetí služeb při selhání pomocí oblasti, ve kterých vaše data se replikují seřazený podle priority. <br><br>Další informace v [regionální převzetí služeb při selhání v Azure Cosmos DB](regional-failover.md).|
|Místní replikaci|I v rámci jednoho datového centra Azure Cosmos DB automaticky replikuje data pro zajištění vysoké dostupnosti, která poskytuje možnost [úrovně konzistence](consistency-levels.md). Zaručí se tím 99,99 % [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/cosmos-db) pro všechny účty jedné oblasti a všechny oblasti s více účty s zmírnit konzistence a 99.999 % čtení dostupnosti pro všechny účty databáze více oblast.|
|Automatizované zálohování online|Azure Cosmos DB databáze jsou pravidelně zálohovány a uloženy v úložišti georedundant. <br><br>Další informace v [automatické online zálohování a obnovení Azure Cosmos DB](online-backup-and-restore.md).|
|Obnovení odstraněných dat|Automatizované zálohování online lze použít k obnovení dat, které jste omylem odstranili až ~ 30 dnů po události. <br><br>Další informace v [automatické online zálohování a obnovení databáze Cosmos Azure](online-backup-and-restore.md)|
|Chránit a izolovat citlivá data|Všechna data v oblasti uvedené v [co je nového?](#whats-new) je nyní v zašifrované podobě.<br><br>Osobní údaje a jiné důvěrné údaje lze izolovat konkrétní kolekce a čtení a zápis, nebo jen pro čtení může být omezen na konkrétní uživatele.|
|Monitorování útokům.|Pomocí [protokolování a aktivity protokoly auditu](logging.md), můžete monitorovat váš účet pro běžné a neobvyklé aktivity. Můžete zobrazit, jaké operace měla provést na vašich prostředků, kteří iniciované operace, při operaci došlo k chybě, stav operace a víc jako zobrazené na snímku obrazovky za touto tabulkou.|
|Reakce na útoky|Jakmile máte kontaktovat podporu Azure o ohlásit možný útok, proces reakcí na incidenty krok 5 je spuštěna. Cílem procesu krok 5 je po došlo k potížím se zamykáním a jestli je spuštěná šetření co nejdříve obnovit normální služby zabezpečení a operace.<br><br>Další informace v [odpověď zabezpečení společnosti Microsoft Azure v cloudu](https://aka.ms/securityresponsepaper).|
|Geografického vymezení|Azure Cosmos DB zajišťuje řízení dat pro svrchovaných oblasti (například Německo, Čína, nám verze pro státní správu).|
|Chráněné pracoviště|Data v Azure Cosmos DB se ukládají na jednotkách SSD v chráněných datových centrech Azure.<br><br>Další informace v [globálních datových centrech společnosti Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Šifrování protokolu HTTPS, SSL/TLS|Všechny interakce klienta služby Azure Cosmos DB jsou podporující protokol SSL/TLS 1.2. Všechny uvnitř datového centra a mezi replikace datacenter je také SSL/TLS 1.2 vynucené.|
|Šifrování v klidovém stavu|Všechna data uložena do Azure Cosmos DB zašifrovaná přinejmenším. Další informace v [Azure Cosmos DB šifrování v klidovém stavu](.\database-encryption-at-rest.md)|
|Opravenou servery|Jako spravované databáze Azure Cosmos DB eliminuje potřebu spravovat a opravovat servery, které bylo dokončeno, automaticky.|
|Účty pro správu s silná hesla|Je obtížné domníváte musíme i zmínili tento požadavek, ale na rozdíl od některých naše konkurenci, je možné mít účet správce bez hesla v Azure Cosmos DB.<br><br> Zabezpečení prostřednictvím protokolu SSL a HMAC tajný ověřování založené na certifikaci je zaručená v ve výchozím nastavení.|
|Zabezpečení a data protection certifikace|Má Azure Cosmos DB [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [klauzule Evropského modelu (VVEU)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses), a [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) certifikace. Další certifikáty jsou v průběhu.|

Následující snímek obrazovky ukazuje integrace služby Active directory (RBAC) pomocí řízení přístupu (IAM) na portálu Azure: ![řízení (IAM) na portálu Azure – ukázka databáze zabezpečení přístupu](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Následující snímek obrazovky ukazuje, jak můžete pomocí protokolů auditu protokolování a aktivity pro monitorování vašeho účtu: ![aktivity protokoly pro Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Další postup

Další podrobnosti o hlavních klíčů a tokeny prostředků najdete v tématu [zabezpečení přístupu k datům v Azure Cosmos DB](secure-access-to-data.md).

Další informace o protokolování auditu najdete v tématu [protokolování diagnostiky Azure Cosmos DB](logging.md).

Další podrobnosti o certifikace společnosti Microsoft najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).
