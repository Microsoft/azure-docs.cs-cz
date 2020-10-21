---
title: Zabezpečení databáze – Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB poskytuje ochranu databáze a zabezpečení dat pro vaše data.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 3658c621a5ac633bf42334df3e354c88afcf9b27
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278795"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpečení ve službě Azure Cosmos DB – Přehled

Tento článek popisuje osvědčené postupy zabezpečení databází a klíčové funkce, které nabízí služba Azure Cosmos DB a které pomáhají s ochranou před porušeními zabezpečení databází, jejich rozpoznáváním a reakcí na ně.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Co je nového v Azure Cosmos DB zabezpečení

Šifrování v klidovém umístění je teď k dispozici pro dokumenty a zálohy uložené v Azure Cosmos DB ve všech oblastech Azure. Šifrování v klidovém umístění se používá automaticky pro nové i stávající zákazníky v těchto oblastech. Není nutné nic konfigurovat. a získáte stejnou skvělou latenci, propustnost, dostupnost a funkčnost, stejně jako v případě, že máte na vědomí, že vaše data jsou bezpečná a zabezpečená pomocí šifrování v klidovém stavu.

## <a name="how-do-i-secure-my-database"></a>Návody zabezpečení databáze

Zabezpečení dat je sdílená odpovědnost mezi vámi, zákazníkem a vaším poskytovatelem databáze. V závislosti na tom, jaký poskytovatel databáze si zvolíte, se může změnit množství zodpovědnosti, které je třeba provést. Pokud zvolíte místní řešení, musíte poskytnout vše od ochrany koncových bodů až po fyzické zabezpečení vašeho hardwaru – což není jednoduchý úkol. Pokud zvolíte poskytovatele cloudové databáze PaaS, jako je například Azure Cosmos DB, vaše oblast obav se podstatně zmenší. Následující obrázek, který se vypůjčil ze [sdílených odpovědností Microsoftu za Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) , ukazuje, jak se vaše zodpovědnost sníží s poskytovatelem PaaS, jako Azure Cosmos DB.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Zodpovědnost za zákazníky a poskytovatele databáze":::

Předchozí diagram ukazuje komponenty cloudového zabezpečení na vysoké úrovni, ale jaké položky si musíte zabývat konkrétně pro vaše databázové řešení? A jak můžete porovnat řešení mezi sebou?

Doporučujeme následující kontrolní seznam požadavků, na které se mají porovnat databázové systémy:

- Nastavení zabezpečení sítě a brány firewall
- Ověřování uživatelů a jemně odstupňované uživatelské ovládací prvky
- Možnost globálně replikovat data pro regionální selhání
- Možnost převzetí služeb při selhání z jednoho datového centra na jiný
- Replikace místních dat v datovém centru
- Automatické zálohování dat
- Obnovení odstraněných dat ze zálohy
- Chraňte a izolujte citlivá data
- Monitorování útoků
- Reakce na útoky
- Schopnost omezit geografickou ochrannou data na dodržování omezení zásad správného řízení dat
- Fyzická ochrana serverů v chráněných datových centrech
- Certifikace

I když se to může zdát zřejmé, nedávné nepostradatelné [porušení databáze](https://thehackernews.com/2017/01/mongodb-database-security.html) nám připomínat jednoduché, ale kritické důležitosti následujících požadavků:

- Aktualizované servery, které jsou v aktuálním stavu
- HTTPS ve výchozím nastavení/šifrování TLS
- Účty pro správu se silnými hesly

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak aplikace Azure Cosmos DB zabezpečit moji databázi

Pojďme se podívat na předchozí seznam – kolik těchto požadavků na zabezpečení Azure Cosmos DB poskytnout? Každé jedno.

Pojďme se na každou z nich dig podrobněji.

|Požadavek zabezpečení|Přístup k zabezpečení Azure Cosmos DB|
|---|---|
|Zabezpečení sítě|Použití brány firewall protokolu IP je první vrstvou ochrany pro zabezpečení vaší databáze. Azure Cosmos DB podporuje řízení přístupu na základě protokolu IP, která jsou řízená pomocí zásad pro podporu brány firewall. Řízení přístupu na základě IP adresy se podobá pravidlům brány firewall, která používají tradiční databázové systémy, ale rozšiřují se, aby účet databáze Azure Cosmos byl přístupný jenom ze schválené sady počítačů nebo cloudových služeb. Další informace najdete v článku o [podpoře Azure Cosmos DB brány firewall](how-to-configure-firewall.md) .<br><br>Azure Cosmos DB vám umožní povolit konkrétní IP adresu (168.61.48.0), rozsah IP adres (168.61.48.0/8) a kombinace IP adres a rozsahů. <br><br>Všechny požadavky pocházející z počítačů mimo tento seznam povolených jsou blokované Azure Cosmos DB. Požadavky ze schválených počítačů a cloudových služeb pak musí dokončit proces ověřování, aby bylo možné k prostředkům udělit řízení přístupu.<br><br> [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md) můžete použít k zajištění izolace sítě a ochraně vašich Azure Cosmos DBch prostředků z obecného internetového prostředí. Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například AzureCosmosDB) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.|
|Authorization (Autorizace)|Azure Cosmos DB pro autorizaci používá algoritmus HMAC (hash-based Message Authentication Code). <br><br>Každý požadavek má pomocí klíče tajného účtu hodnotu hash a při každém volání Azure Cosmos DB se pošle následná hodnota hash kódovaná pomocí základního-64. Pro ověření žádosti služba Azure Cosmos DB používá správný tajný klíč a vlastnosti k vygenerování hodnoty hash, a pak porovná hodnotu s hodnotou v žádosti. Pokud se dvě hodnoty shodují, operace se úspěšně autorizuje a zpracuje se. v opačném případě dojde k chybě autorizace a žádost se odmítne.<br><br>Můžete použít buď [primární klíč](secure-access-to-data.md#primary-keys), nebo [token prostředku](secure-access-to-data.md#resource-tokens) , který umožňuje jemně odstupňovaný přístup k prostředku, jako je například dokument.<br><br>Další informace najdete v [zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Uživatelé a oprávnění|Pomocí primárního klíče pro účet můžete vytvořit prostředky uživatelů a prostředky oprávnění na databázi. Token prostředku je přidružen k oprávnění v databázi a určuje, zda má uživatel přístup k prostředku aplikace v databázi (čtení i zápis, jen pro čtení nebo bez přístupu). Mezi prostředky aplikace patří kontejner, dokumenty, přílohy, uložené procedury, triggery a UDF. Token prostředku se pak při ověřování použije k poskytnutí nebo zamítnutí přístupu k prostředku.<br><br>Další informace najdete v [zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Integrace služby Active Directory (RBAC)| Pomocí řízení přístupu (IAM) v Azure Portal můžete také poskytnout nebo omezit přístup k účtu Cosmos, databázi, kontejneru a nabídkám (propustnost). IAM poskytuje řízení přístupu na základě role a integruje se se službou Active Directory. Můžete použít předdefinované role nebo vlastní role pro jednotlivce a skupiny. Další informace najdete v článku věnovaném [integraci služby Active Directory](role-based-access-control.md) .|
|Globální replikace|Azure Cosmos DB nabízí globální distribuci klíč, která umožňuje replikovat data do libovolného datacentra Azure v celém světě pomocí kliknutí na tlačítko. Globální replikace umožňuje horizontální škálování a přístup k datům po celém světě a zajišťuje tak přístup s nízkou latencí.<br><br>Globální replikace v souvislosti se zabezpečením zajišťuje ochranu dat před místními chybami.<br><br>Další informace najdete v části [Globální distribuce dat](distribute-data-globally.md).|
|Místní převzetí služeb při selhání|Pokud jste svá data replikoval ve více než jednom datovém centru, Azure Cosmos DB se automaticky převezmou na vaše operace, aby místní datové centrum bylo online. Seznam oblastí převzetí služeb při selhání můžete vytvořit pomocí oblastí, ve kterých se vaše data replikují. <br><br>Další informace najdete v oblasti [místní převzetí služeb při selhání v Azure Cosmos DB](high-availability.md).|
|Místní replikace|I v rámci jednoho datového centra Azure Cosmos DB automaticky replikuje data pro zajištění vysoké dostupnosti, což vám umožní vybrat si [úrovně konzistence](consistency-levels.md). Tato replikace garantuje [smlouvu SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 99,99% dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s odlehčenou konzistencí a 99,999% dostupností pro všechny účty databáze ve více oblastech.|
|Automatizované online zálohování|Databáze Azure Cosmos se pravidelně zálohují a ukládají se v geograficky redundantním úložišti. <br><br>Další informace najdete v [automatickém online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md).|
|Obnovit Odstraněná data|Automatické zálohování online lze použít k obnovení dat, která jste pravděpodobně omylem odstranili až do 30 dnů od události. <br><br>Další informace najdete v [automatickém online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md)|
|Chraňte a izolujte citlivá data|Všechna data v oblastech uvedených v části Co je nového? je teď zašifrovaný v klidovém stavu.<br><br>Osobní údaje a další důvěrné údaje mohou být izolované na konkrétního kontejneru a přístup pro čtení i zápis, nebo jen pro čtení, mohou být omezeny na konkrétní uživatele.|
|Monitorování útoků|Pomocí protokolu [auditu a protokolů aktivit](logging.md)můžete monitorovat účet pro normální a neobvyklé aktivity. Můžete si prohlédnout, jaké operace byly provedeny na vašich prostředcích, kteří operaci zahájili, kdy k operaci došlo, na stavu operace a mnohem více, jak je znázorněno na snímku obrazovky, který následuje v této tabulce.|
|Reakce na útoky|Po kontaktování podpory Azure za účelem nahlášení potenciálního útoku se aktivuje proces reakce na případ 5 kroků. Cílem procesu 5 kroků je obnovit normální zabezpečení a provoz služeb co nejrychleji po zjištění problému a spuštění šetření.<br><br>Další informace najdete v [Microsoft Azure odpovědi na zabezpečení v cloudu](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geografické oplocení|Azure Cosmos DB zajišťuje zásady správného řízení dat pro oblasti svrchovan (například Německo, Čína, US Gov).|
|Chráněná zařízení|Data v Azure Cosmos DB se ukládají na SSD v chráněných datových centrech Azure.<br><br>Další informace najdete v [globálním datacentru Microsoftu](https://www.microsoft.com/en-us/cloud-platform/global-datacenters) .|
|Šifrování HTTPS/SSL/TLS|Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také protokol TLS 1,2.<br>Je možné vyhovět minimální verzi TLS na straně serveru. Pokud to chcete udělat, kontaktujte prosím [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .|
|Šifrování neaktivních uložených dat|Všechna data uložená v Azure Cosmos DB jsou v klidovém stavu šifrovaná. Další informace najdete v [Azure Cosmos DB šifrování v klidovém umístění](./database-encryption-at-rest.md) .|
|Opravené servery|Jako spravovaná databáze Azure Cosmos DB eliminuje nutnost spravovat a opravovat servery, které jsou pro vás hotové, automaticky.|
|Účty pro správu se silnými hesly|Je těžké se domnívat, že tento požadavek potřebujeme, ale na rozdíl od některých z našich konkurence není možné mít účet správce bez hesla v Azure Cosmos DB.<br><br> Zabezpečení prostřednictvím ověřování TLS a tajného klíče HMAC je ve výchozím nastavení vloženými.|
|Certifikace zabezpečení a ochrany dat| Nejaktuálnější seznam certifikací najdete na celkovém [webu dodržování předpisů Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) a v nejnovějším [dokumentu o dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) všemi certifikacemi (vyhledejte Cosmos). Další informace najdete v tématu 25. dubna 2018 post [Azure #CosmosDB: Secure, Private, splňující předpisy, která zahrnuje SOC 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High a spoustu dalších.

Následující snímek obrazovky ukazuje, jak můžete pomocí protokolu auditu a protokolů aktivit monitorovat svůj účet: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="Zodpovědnost za zákazníky a poskytovatele databáze":::

## <a name="next-steps"></a>Další kroky

Další informace o primárních klíčích a tokenech prostředků najdete v tématu [zabezpečení přístupu k Azure Cosmos DB dat](secure-access-to-data.md).

Další informace o protokolování auditu najdete v tématu [Azure Cosmos DB protokolování diagnostiky](logging.md).

Další informace o certifikaci Microsoftu najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).
