---
title: Zabezpečení databáze – Azure Cosmos DB
description: Zjistěte, jak službu Azure Cosmos DB poskytuje ochranu a data zabezpečení pro vaše data databáze.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0c97d70ea3e5c7fdd14b0f97c5e393359f2b948e
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087227"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpečení v Azure Cosmos DB – přehled

Tento článek popisuje osvědčené postupy zabezpečení databází a klíčové funkce, které nabízí služba Azure Cosmos DB a které pomáhají s ochranou před porušeními zabezpečení databází, jejich rozpoznáváním a reakcí na ně.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Co je nového v Azure Cosmos DB zabezpečení

Šifrování v klidovém stavu je nyní k dispozici pro dokumenty a zálohy uložené ve službě Azure Cosmos DB ve všech oblastech Azure. Šifrování v klidovém stavu je automaticky použita pro nové i stávající zákazníky v těchto oblastech. Není potřeba cokoli konfigurovat získáte stejné skvělé latence, propustnosti, dostupnosti a funkce jako před s výhodou poskytovatelů znalost vašich dat je bezpečným a zabezpečeným pomocí šifrování v klidovém stavu.

## <a name="how-do-i-secure-my-database"></a>Návody zabezpečení databáze

Zabezpečení dat je sdílenou odpovědnost mezi vámi, zákazníka a poskytovatele databáze. Velikost odpovědnosti, které se provádějí může lišit v závislosti na poskytovateli databáze, kterou zvolíte. Pokud vyberete možnost místního řešení, budete muset zajistí všechno, co z ochranu koncových bodů k fyzickému zabezpečení hardwaru – tedy žádná snadný úkol. Pokud se rozhodnete poskytovatele cloudové databáze PaaS jako je Azure Cosmos DB, výrazně zmenší vaší oblasti zájmu. Následující obrázek, který se vypůjčil ze [sdílených odpovědností Microsoftu za Cloud Computing](https://aka.ms/sharedresponsibility) , ukazuje, jak se vaše zodpovědnost sníží s poskytovatelem PaaS, jako Azure Cosmos DB.

![Odpovědnosti zákazníka a databáze zprostředkovatele](./media/database-security/nosql-database-security-responsibilities.png)

Předchozí diagram znázorňuje základní cloudovou součásti zabezpečení, ale položky, které je potřeba si dělat starosti speciálně pro vaše řešení databáze? A jak můžeme srovnávat řešení k sobě navzájem?

Doporučujeme následující kontrolní seznam požadavků, na kterém bude probíhat porovnávání databázové systémy:

- Nastavení brány firewall a zabezpečení sítě
- Ověřování uživatelů a pokud potřebujete jemněji odstupňované uživatelské ovládací prvky
- Možnost replikovat data globálně pro selháním v dané oblasti
- Možnost převzetí služeb při selhání z jednoho datového centra na jiný
- Replikace místních dat v rámci datového centra
- Automatická data zálohy
- Obnovení odstraněných dat ze zálohy
- Ochrana a izolovat citlivá data
- Monitorování pro útoky
- Reakce na útoky
- Možnost geo ochranná data dodržovat omezení zásad správného řízení dat
- Fyzická ochrana servery v centrech chráněná data
- Certifikace

I když se to může zdát zřejmé, nedávné nepostradatelné [porušení databáze](https://thehackernews.com/2017/01/mongodb-database-security.html) nám připomínat jednoduché, ale kritické důležitosti následujících požadavků:

- Aktualizované servery, které jsou v aktuálním stavu
- Protokol HTTPS pomocí šifrování výchozí/SSL
- Účty pro správu s silná hesla

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak aplikace Azure Cosmos DB zabezpečit moji databázi

Pojďme se podívat zpět v předchozím seznamu – kolik tyto požadavky na zabezpečení služby Azure Cosmos DB poskytuje? Každý jeden.

Pojďme se podívat do každé z nich podrobněji.

|Požadavek na zabezpečení|Postup zabezpečení služby Azure Cosmos DB|
|---|---|
|Zabezpečení sítě|Použití brány firewall protokolu IP je první vrstvu ochrany a zabezpečení vaší databáze. Azure Cosmos DB podporuje zásady řízení přístupu na základě IP adresy pro podporu brány firewall pro příchozí řízené. Řízení přístupu na základě IP adresy se podobá pravidlům brány firewall, která používají tradiční databázové systémy, ale rozšiřují se, aby účet databáze Azure Cosmos byl přístupný jenom ze schválené sady počítačů nebo cloudových služeb. Další informace najdete v článku o [podpoře Azure Cosmos DB brány firewall](firewall-support.md) .<br><br>Azure Cosmos DB umožňuje povolit konkrétní IP adresu (168.61.48.0), rozsah IP adres (168.61.48.0/8) a kombinace IP adresy a rozsahy adres. <br><br>Služba Azure Cosmos DB jsou blokovány všechny požadavky z počítačů mimo tento seznam povolených. Požadavky ze schválených počítače a cloudové služby pak musíte dokončit proces ověřování má být poskytnut řízení přístupu k prostředkům.<br><br> [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md) můžete použít k zajištění izolace sítě a ochraně vašich Azure Cosmos DBch prostředků z obecného internetového prostředí. Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například AzureCosmosDB) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.|
|Autorizace|Azure Cosmos DB používá ověřovací kód zprávy na základě hodnoty hash (HMAC) pro autorizaci. <br><br>Každý požadavek je zakódována pomocí tajný klíč a následné base-64 kódovaných hash posílá se každé volání do služby Azure Cosmos DB. Pro ověření žádosti, služby Azure Cosmos DB používá správný tajný klíč a vlastnosti ke generování hodnoty hash a poté porovnává hodnotu se v žádosti. Pokud tyto dvě hodnoty odpovídají, je operace úspěšná autorizace a zpracuje požadavek, jinak je chybě autorizace a žádost se zamítá.<br><br>Můžete použít buď [hlavní klíč](secure-access-to-data.md#master-keys), nebo [token prostředku](secure-access-to-data.md#resource-tokens) , který umožňuje jemně odstupňovaný přístup k prostředku, jako je například dokument.<br><br>Další informace najdete v [zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Uživatele a oprávnění|Pomocí hlavního klíče pro účet můžete vytvořit prostředky uživatelů a prostředky oprávnění na databázi. Token prostředku je přidružen k oprávnění v databázi a určuje, zda má uživatel přístup k prostředku aplikace v databázi (čtení i zápis, jen pro čtení nebo bez přístupu). Prostředky aplikace obsahují kontejneru, dokumenty, přílohy, uložené procedury, triggery a uživatelem definovanými funkcemi. Token prostředku se pak použije při ověřování zadat nebo odepřít přístup k prostředku.<br><br>Další informace najdete v [zabezpečení přístupu k prostředkům Azure Cosmos DB](secure-access-to-data.md).|
|Integrace služby Active directory (RBAC)| Pomocí řízení přístupu (IAM) v Azure Portal můžete také poskytnout nebo omezit přístup k účtu Cosmos, databázi, kontejneru a nabídkám (propustnost). IAM poskytuje řízení přístupu na základě rolí a integruje se službou Active Directory. Můžete použít předdefinované role nebo vlastní role pro jednotlivce a skupiny. Další informace najdete v článku věnovaném [integraci služby Active Directory](role-based-access-control.md) .|
|Globální replikace|Azure Cosmos DB nabízí globální distribuce na klíč, který umožňuje replikovat data do kterékoli z datových center Azure na celém světě s kliknutím na tlačítko. Globální replikace vám umožní škálovat globálně a zajistit přístup s nízkou latencí k datům po celém světě.<br><br>Globální replikace v kontextu zabezpečení, zajišťuje ochranu dat před selháním v dané oblasti.<br><br>Další informace najdete v článku o [globální distribuci dat](distribute-data-globally.md).|
|Regionální převzetí služeb při selhání|Pokud máte replikuje vaše data ve více než jedno datové centrum, Azure Cosmos DB automaticky navyšování vaše operace by měla datovým centrem přejdou do režimu offline. Můžete vytvořit seznam seřazený podle priority převzetí služeb při selhání oblasti použití oblastí, ve kterých se replikuje vaše data. <br><br>Další informace najdete v oblasti [místní převzetí služeb při selhání v Azure Cosmos DB](high-availability.md).|
|Místní replikaci|I v rámci jednoho datového centra Azure Cosmos DB automaticky replikuje data pro zajištění vysoké dostupnosti, což vám umožní vybrat si [úrovně konzistence](consistency-levels.md). Tato replikace garantuje [smlouvu SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 99,99% dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s odlehčenou konzistencí a 99,999% dostupností pro všechny účty databáze ve více oblastech.|
|Automatizované zálohování online|Databáze Azure Cosmos se pravidelně zálohují a ukládají se v geograficky redundantním úložišti. <br><br>Další informace najdete v [automatickém online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md).|
|Obnovení odstraněných dat|Automatizované zálohování online je možné obnovit data, možná jste omylem odstranili až ~ 30 dnů po skončení události. <br><br>Další informace najdete v [automatickém online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md)|
|Ochrana a izolovat citlivá data|Všechna data v oblastech uvedených v části Co je nového? je teď zašifrovaný v klidovém stavu.<br><br>Osobní údaje a další důvěrná data můžou být izolované na konkrétní kontejner a čtení a zápis nebo přístup jen pro čtení může být omezen na konkrétní uživatele.|
|Monitorování pro útoky|Pomocí protokolu [auditu a protokolů aktivit](logging.md)můžete monitorovat účet pro normální a neobvyklé aktivity. Můžete zobrazit, jaké operace provedených s vašimi prostředky, kteří při operaci došlo k chybě, stav operace a mnohem více jak je znázorněno na snímku obrazovky touto tabulkou, který operaci spustil.|
|Reakce na útoky|Jakmile máte kontaktovat podporu Azure o ohlásit možný útok, proces reakcí na incidenty krok 5 je spuštěna. Cíl 5 krocích se má obnovit normální provoz zabezpečení a operace co nejrychleji po zjištění problému a spustit šetření.<br><br>Další informace najdete v [Microsoft Azure odpovědi na zabezpečení v cloudu](https://aka.ms/securityresponsepaper).|
|Geograficky monitorování geografických zón|Azure Cosmos DB zajišťuje řízení dat pro suverénní oblasti (například Germany, Čína, státní správy USA).|
|Chráněné zařízení|Data ve službě Azure Cosmos DB se ukládají na jednotkách SSD v chráněných datových centrech Azure.<br><br>Další informace najdete v [globálním datacentru Microsoftu](https://www.microsoft.com/en-us/cloud-platform/global-datacenters) .|
|Šifrování protokolu HTTPS a SSL/TLS|Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také protokol TLS 1,2.<br>Je možné vyhovět minimální verzi TLS na straně serveru. Pokud to chcete udělat, kontaktujte prosím [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Šifrování v klidovém stavu|Všechna data ukládají do služby Azure Cosmos DB se šifrují při nečinnosti. Další informace najdete v [Azure Cosmos DB šifrování v klidovém umístění](./database-encryption-at-rest.md) .|
|Patched servery|Jako spravovanou databázovou službu Azure Cosmos DB eliminuje potřebu správu a servery, oprava, která má automaticky provede za vás.|
|Účty pro správu s silná hesla|Je těžké si myslíte, že musíme ještě zmiňovat tento požadavek, ale na rozdíl od některých z našich konkurentů, není možné mít účet správce bez hesla ve službě Azure Cosmos DB.<br><br> Ve výchozím nastavení je těšte zabezpečení prostřednictvím protokolu SSL a HMAC tajného kódu na základě ověřování.|
|Zabezpečení a data protection certifikace| Nejaktuálnější seznam certifikací najdete na celkovém [webu dodržování předpisů Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) a v nejnovějším [dokumentu o dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) všemi certifikacemi (vyhledejte Cosmos). Další informace najdete v tématu 25. dubna 2018 post [Azure #CosmosDB: Secure, Private, splňující předpisy, která zahrnuje SOC 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High a spoustu dalších.

Následující snímek obrazovky ukazuje, jak můžete pomocí protokolu auditu a protokolů aktivit monitorovat svůj účet: ![protokoly aktivit pro Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Další kroky

Další informace o hlavních klíčích a tokenech prostředků najdete v tématu [zabezpečení přístupu k Azure Cosmos DB dat](secure-access-to-data.md).

Další informace o protokolování auditu najdete v tématu [Azure Cosmos DB protokolování diagnostiky](logging.md).

Další informace o certifikaci Microsoftu najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).