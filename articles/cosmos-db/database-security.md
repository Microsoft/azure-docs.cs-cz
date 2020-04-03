---
title: Zabezpečení databáze – Azure Cosmos DB
description: Zjistěte, jak Azure Cosmos DB poskytuje ochranu databáze a zabezpečení dat pro vaše data.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 345fc329df1c57cab7dd66c609bf3701fa3a6124
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619130"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpečení ve službě Azure Cosmos DB – Přehled

Tento článek popisuje osvědčené postupy zabezpečení databází a klíčové funkce, které nabízí služba Azure Cosmos DB a které pomáhají s ochranou před porušeními zabezpečení databází, jejich rozpoznáváním a reakcí na ně.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Co je nového v zabezpečení Azure Cosmos DB

Šifrování v klidovém stavu je teď dostupné pro dokumenty a zálohy uložené v Azure Cosmos DB ve všech oblastech Azure. Šifrování v klidovém stavu se použije automaticky pro nové i stávající zákazníky v těchto oblastech. Není třeba nic konfigurovat; a získáte stejnou skvělou latenci, propustnost, dostupnost a funkčnost jako dříve s výhodou vědomí, že vaše data jsou v bezpečí díky šifrování v klidovém stavu.

## <a name="how-do-i-secure-my-database"></a>Jak zabezpečit svou databázi

Zabezpečení dat je sdílená odpovědnost mezi vámi, zákazníkem a poskytovatelem databáze. V závislosti na zvoleném poskytovateli databáze se může množství odpovědnosti lišit. Pokud zvolíte místní řešení, musíte poskytnout vše od ochrany koncových bodů až po fyzické zabezpečení hardwaru , což není snadný úkol. Pokud zvolíte poskytovatele cloudové databáze PaaS, jako je Azure Cosmos DB, vaše oblast zájmu se značně zmenší. Následující obrázek vypůjčený z dokumentu white [paper o sdílených odpovědnostech microsoftu pro cloud computing](https://aka.ms/sharedresponsibility) ukazuje, jak se vaše odpovědnost snižuje s poskytovatelem PaaS, jako je Azure Cosmos DB.

![Odpovědnosti zákazníků a poskytovatelů databází](./media/database-security/nosql-database-security-responsibilities.png)

Předchozí diagram zobrazuje součásti zabezpečení cloudu vysoké úrovně, ale jaké položky je třeba se obávat konkrétně pro databázové řešení? A jak můžete vzájemně porovnávat řešení?

Doporučujeme následující kontrolní seznam požadavků, na kterých chcete porovnat databázové systémy:

- Nastavení zabezpečení sítě a brány firewall
- Ověřování uživatelů a jemně odstupňované uživatelské ovládací prvky
- Schopnost replikovat data globálně pro regionální selhání
- Možnost převzetí služeb při selhání z jednoho datového centra do druhého
- Replikace místních dat v datovém centru
- Automatické zálohování dat
- Obnovení smazaných dat ze záloh
- Ochrana a izolování citlivých dat
- Sledování útoků
- Reakce na útoky
- Schopnost geo-fence data dodržovat omezení správy dat
- Fyzická ochrana serverů v chráněných datových centrech
- Certifikace

A i když se to může zdát zřejmé, nedávné [rozsáhlé narušení databáze](https://thehackernews.com/2017/01/mongodb-database-security.html) nám připomínají jednoduchý, ale kritický význam následujících požadavků:

- Opravené servery, které jsou průběžně aktuální
- HTTPS ve výchozím nastavení/Šifrování TLS
- Účty pro správu se silnými hesly

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak Azure Cosmos DB zabezpečuje mou databázi

Podívejme se zpět na předchozí seznam – kolik z těchto požadavků na zabezpečení poskytuje Azure Cosmos DB? Do jednoho.

Pojďme se podívat do každého z nich podrobně.

|Požadavek na zabezpečení|Přístup k zabezpečení Azure Cosmos DB|
|---|---|
|Zabezpečení sítě|Použití brány firewall IP je první vrstvou ochrany pro zabezpečení databáze. Azure Cosmos DB podporuje zásady založené na ip přístupu pro příchozí podporu brány firewall. Ovládací prvky přístupu založené na protokolu IP jsou podobné pravidlům brány firewall používaným tradičními databázovými systémy, ale jsou rozbaleny tak, aby databázový účet Azure Cosmos byl přístupný jenom ze schválené sady počítačů nebo cloudových služeb. Další informace najdete v článku [o podpoře brány firewall Azure Cosmos DB.](firewall-support.md)<br><br>Azure Cosmos DB umožňuje povolit konkrétní IP adresu (168.61.48.0), rozsah IP adres (168.61.48.0/8) a kombinace IP adres a rozsahů. <br><br>Všechny požadavky pocházející z počítačů mimo tento seznam povolených jsou blokovány Službou Azure Cosmos DB. Požadavky ze schválených počítačů a cloudových služeb pak musí dokončit proces ověřování, které mají být poskytnuty řízení přístupu k prostředkům.<br><br> [Značky virtuálních síťových služeb](../virtual-network/service-tags-overview.md) můžete použít k dosažení izolace sítě a ochraně prostředků Azure Cosmos DB před obecným Internetem. Při vytváření pravidel zabezpečení používejte značky služeb místo konkrétních adres IP. Zadáním názvu značky služby (například AzureCosmosDB) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu.|
|Autorizace|Azure Cosmos DB používá pro autorizaci ověřovací kód zpráv založený na hash (HMAC). <br><br>Každý požadavek je zakódován pomocí klíče tajného účtu a následné základní-64 kódované hash je odeslána s každým voláním Do Azure Cosmos DB. K ověření požadavku používá služba Azure Cosmos DB správný tajný klíč a vlastnosti ke generování hodnoty hash, pak porovná hodnotu s hodnotou v požadavku. Pokud se obě hodnoty shodují, operace je úspěšně autorizována a požadavek je zpracován, jinak dojde k selhání autorizace a požadavek je odmítnut.<br><br>Můžete použít [hlavní klíč](secure-access-to-data.md#master-keys)nebo [token prostředku](secure-access-to-data.md#resource-tokens) umožňující jemně odstupňovaný přístup k prostředku, jako je například dokument.<br><br>Další informace najdete v [informacích zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Uživatelé a oprávnění|Pomocí hlavního klíče pro účet můžete vytvořit uživatelské prostředky a prostředky oprávnění pro databázi. Token prostředku je přidružen k oprávnění v databázi a určuje, zda má uživatel přístup (jen pro čtení, jen pro čtení nebo žádný přístup) k prostředku aplikace v databázi. Prostředky aplikace zahrnují kontejner, dokumenty, přílohy, uložené procedury, aktivační události a ufl. Token prostředku se pak používá při ověřování k poskytnutí nebo odepření přístupu k prostředku.<br><br>Další informace najdete v [informacích zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Integrace active adresáře (RBAC)| Můžete také poskytnout nebo omezit přístup k účtu Cosmos, databázi, kontejneru a nabídek (propustnost) pomocí řízení přístupu (IAM) na webu Azure Portal. IAM poskytuje řízení přístupu na základě rolí a integruje se službou Active Directory. Můžete použít integrované role nebo vlastní role pro jednotlivce a skupiny. Další informace naleznete v článku [integrace služby Active Directory.](role-based-access-control.md)|
|Globální replikace|Azure Cosmos DB nabízí globální distribuci na klíč, která umožňuje replikovat data do některého z celosvětově datových center Azure kliknutím na tlačítko. Globální replikace umožňuje globální škálování a poskytuje přístup k datům s nízkou latencí po celém světě.<br><br>V kontextu zabezpečení globální replikace zajišťuje ochranu dat před regionálními selháními.<br><br>Další informace najdete v části [Globální distribuce dat](distribute-data-globally.md).|
|Místní převzetí služeb při selhání|Pokud jste replikovali data ve více než jednom datovém centru, Azure Cosmos DB se automaticky převrátí přes vaše operace, pokud regionální datové centrum přejde do režimu offline. Pomocí oblastí, ve kterých jsou data replikována, můžete vytvořit seznam oblastí s prioritou převzetí služeb při selhání. <br><br>Další informace o [místních převzetích služeb při selhání v Azure Cosmos DB](high-availability.md).|
|Místní replikace|I v rámci jednoho datového centra Azure Cosmos DB automaticky replikuje data pro vysokou dostupnost, což vám dává možnost volby [úrovní konzistence](consistency-levels.md). Tato replikace zaručuje 99,99% [dostupnost sla](https://azure.microsoft.com/support/legal/sla/cosmos-db) pro všechny účty s jednou oblastí a všechny účty s více oblastmi s uvolněnou konzistencí a 99,999% dostupnost čtení na všech databázových účtech s více oblastmi.|
|Automatické zálohování online|Databáze Azure Cosmos se pravidelně zálohují a ukládají v geograficky redundantním úložišti. <br><br>Další informace najdete v [oblasti Automatické zálohování a obnovení online pomocí Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).|
|Obnovení odstraněných dat|Automatické zálohování online lze použít k obnovení dat, která jste omylem odstranili až do ~30 dnů po události. <br><br>Další informace v [oblasti Automatické zálohování a obnovení online pomocí Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)|
|Ochrana a izolování citlivých dat|Všechna data v oblastech uvedených v části Co je nového? je nyní zašifrován v klidu.<br><br>Osobní údaje a další důvěrné údaje mohou být izolovány pro konkrétní kontejner a pro čtení a zápis nebo přístup jen pro čtení může být omezen na konkrétní uživatele.|
|Sledování útoků|Pomocí [protokolování auditování a protokolů aktivit](logging.md)můžete sledovat, že váš účet má normální a neobvyklou aktivitu. Můžete zobrazit, jaké operace byly provedeny na vašich prostředcích, kdo operaci inicioval, kdy došlo k operaci, stav operace a mnohem více, jak je znázorněno na snímku obrazovky za touto tabulkou.|
|Reakce na útoky|Jakmile kontaktujete podporu Azure a nahlásíte potenciální útok, zakopne se proces reakce na incidenty v 5 krocích. Cílem procesu v 5 krocích je co nejrychleji obnovit normální zabezpečení a provoz služby po zjištění problému a zahájení šetření.<br><br>Další informace najdete v [části Microsoft Azure Security Response v cloudu](https://aka.ms/securityresponsepaper).|
|Geografické oplocení|Azure Cosmos DB zajišťuje zásady správného řízení dat pro suverénní oblasti (například Německo, Čína, USA Gov).|
|Chráněná zařízení|Data v Azure Cosmos DB se ukládají na ssd discích v chráněných datových centrech Azure.<br><br>Další informace v [globálních datových centrech Microsoftu](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Šifrování HTTPS/SSL/TLS|Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB také podporuje TLS 1.2.<br>Je možné vynutit minimální verzi TLS na straně serveru. Chcete-li tak [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)učinit, obraťte se na společnost .|
|Šifrování v klidovém stavu|Všechna data uložená do Azure Cosmos DB se šifrují v klidovém stavu. Další informace o [šifrování Azure Cosmos DB v klidovém stavu](./database-encryption-at-rest.md)|
|Opravené servery|Jako spravovaná databáze Azure Cosmos DB eliminuje potřebu spravovat a opravovat servery, což se dělá za vás, automaticky.|
|Účty pro správu se silnými hesly|Je těžké uvěřit, že je třeba dokonce zmínit tento požadavek, ale na rozdíl od některých našich konkurentů, je nemožné mít účet pro správu bez hesla v Azure Cosmos DB.<br><br> Zabezpečení prostřednictvím ověřování na základě tajných kódů TLS a HMAC je ve výchozím nastavení.|
|Certifikace zabezpečení a ochrany dat| Nejaktuálnější seznam certifikací najdete na celkovém [webu dodržování předpisů Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) a také na nejnovějším dokumentu o dodržování předpisů [Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) se všemi certifikacemi (vyhledejte Cosmos). Pro více zaměřené přečtěte si podívejte se na 25 dubna 2018 post [Azure #CosmosDB: Secure, private, kompatibilní, který zahrnuje SOCS 1/2 Typ 2, HITRUST, PCI DSS Úroveň 1, ISO 27001, HIPAA, FedRAMP High a mnoho dalších.

Následující snímek obrazovky ukazuje, jak můžete ke sledování svého ![účtu pomocí protokolů protokolů auditu a protokolů aktivit: Protokoly aktivit pro Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Další kroky

Další informace o hlavních klíčích a tokenech prostředků najdete [v tématu Zabezpečení přístupu k datům služby Azure Cosmos DB](secure-access-to-data.md).

Další informace o protokolování auditu najdete v [tématu protokolování diagnostiky Azure Cosmos DB](logging.md).

Další informace o certifikacích Microsoftu najdete [v tématu Azure Trust Center](https://azure.microsoft.com/support/trust-center/).