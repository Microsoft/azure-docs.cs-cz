---
title: Zabezpečení databáze – Azure Cosmos DB
description: Zjistěte, jak službu Azure Cosmos DB poskytuje ochranu a data zabezpečení pro vaše data databáze.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: rimman
ms.openlocfilehash: 3d05da5f62a076dc168bef029cd0babc3946ee6b
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243140"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpečení ve službě Azure Cosmos DB – přehled

Tento článek popisuje osvědčené postupy zabezpečení databáze a klíčových funkcí, které nabízí služba Azure Cosmos DB můžete zabránit, detekci a reakce na porušení databáze.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Co je nového ve službě Azure Cosmos DB zabezpečení?

Šifrování v klidovém stavu je nyní k dispozici pro dokumenty a zálohy uložené ve službě Azure Cosmos DB ve všech oblastech Azure. Šifrování v klidovém stavu je automaticky použita pro nové i stávající zákazníky v těchto oblastech. Není potřeba cokoli konfigurovat získáte stejné skvělé latence, propustnosti, dostupnosti a funkce jako před s výhodou poskytovatelů znalost vašich dat je bezpečným a zabezpečeným pomocí šifrování v klidovém stavu.

## <a name="how-do-i-secure-my-database"></a>Jak zabezpečím Moje databáze? 

Zabezpečení dat je sdílenou odpovědnost mezi vámi, zákazníka a poskytovatele databáze. Velikost odpovědnosti, které se provádějí může lišit v závislosti na poskytovateli databáze, kterou zvolíte. Pokud vyberete možnost místního řešení, budete muset zajistí všechno, co z ochranu koncových bodů k fyzickému zabezpečení hardwaru – tedy žádná snadný úkol. Pokud se rozhodnete poskytovatele cloudové databáze PaaS jako je Azure Cosmos DB, výrazně zmenší vaší oblasti zájmu. Na následujícím obrázku si od společnosti Microsoft [sdílet odpovědnost za Cloud Computing](https://aka.ms/sharedresponsibility) dokument white paper, ukazuje, jak vás, snižuje s poskytovatelem PaaS jako Azure Cosmos DB.

![Odpovědnosti zákazníka a databáze zprostředkovatele](./media/database-security/nosql-database-security-responsibilities.png)

Předchozí diagram znázorňuje základní cloudovou součásti zabezpečení, ale položky, které je potřeba si dělat starosti speciálně pro vaše řešení databáze? A jak můžeme srovnávat řešení k sobě navzájem? 

Doporučujeme následující kontrolní seznam požadavků, na kterém bude probíhat porovnávání databázové systémy:

- Nastavení brány firewall a zabezpečení sítě
- Ověřování uživatelů a pokud potřebujete jemněji odstupňované uživatelské ovládací prvky
- Možnost replikovat data globálně pro selháním v dané oblasti
- Schopnost provádět převzetí služeb při selhání z jednoho datového centra do jiného
- Replikace místních dat v rámci datového centra
- Automatická data zálohy
- Obnovení odstraněných dat ze zálohy
- Ochrana a izolovat citlivá data
- Monitorování pro útoky
- Reakce na útoky
- Možnost geo ochranná data dodržovat omezení zásad správného řízení dat
- Fyzická ochrana servery v centrech chráněná data
- Certifikace

A i když to může zdát zřejmé, poslední [rozsáhlé databáze porušení](https://thehackernews.com/2017/01/mongodb-database-security.html) připomene nám jednoduchý, ale důležité důležitost následující požadavky:
- Opravit servery, které jsou zachovány aktuální.
- Protokol HTTPS pomocí šifrování výchozí/SSL
- Účty pro správu s silná hesla

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak zabezpečit službu Azure Cosmos DB Moje databáze?

Pojďme se podívat zpět v předchozím seznamu – kolik tyto požadavky na zabezpečení služby Azure Cosmos DB poskytuje? Každý jeden.

Pojďme se podívat do každé z nich podrobněji.

|Požadavek na zabezpečení|Postup zabezpečení služby Azure Cosmos DB|
|---|---|---|
|Zabezpečení sítě|Použití brány firewall protokolu IP je první vrstvu ochrany a zabezpečení vaší databáze. Azure Cosmos DB podporuje zásady řízení přístupu na základě IP adresy pro podporu brány firewall pro příchozí řízené. Řízení přístupu na základě IP adresy jsou podobné pravidla brány firewall tradiční databázové systémy, ale jsou rozbaleny tak, aby k účtu databáze Azure Cosmos DB je přístupná pouze ze schválenou sadu počítačů nebo cloudových služeb. <br><br>Azure Cosmos DB umožňuje povolit konkrétní IP adresu (168.61.48.0), rozsah IP adres (168.61.48.0/8) a kombinace IP adresy a rozsahy adres. <br><br>Služba Azure Cosmos DB jsou blokovány všechny požadavky z počítačů mimo tento seznam povolených. Požadavky ze schválených počítače a cloudové služby pak musíte dokončit proces ověřování má být poskytnut řízení přístupu k prostředkům.<br><br>Další informace najdete v [podpora brány firewall služby Azure Cosmos DB](firewall-support.md).|
|Autorizace|Azure Cosmos DB používá ověřovací kód zprávy na základě hodnoty hash (HMAC) pro autorizaci. <br><br>Každý požadavek je zakódována pomocí tajný klíč a následné base-64 kódovaných hash posílá se každé volání do služby Azure Cosmos DB. Pro ověření žádosti, služby Azure Cosmos DB používá správný tajný klíč a vlastnosti ke generování hodnoty hash a poté porovnává hodnotu se v žádosti. Pokud tyto dvě hodnoty odpovídají, je operace úspěšná autorizace a zpracuje požadavek, jinak je chybě autorizace a žádost se zamítá.<br><br>Můžete použít buď [hlavní klíč](secure-access-to-data.md#master-keys), nebo [tokenu prostředku](secure-access-to-data.md#resource-tokens) umožňuje velice přesně kontrolovat přístup k prostředku, jako je například dokument.<br><br>Další informace najdete v [zabezpečení přístupu k prostředkům služby Azure Cosmos DB](secure-access-to-data.md).|
|Uživatele a oprávnění|Použití hlavního klíče pro účet, můžete vytvořit uživatele a oprávnění prostředků na databázi. Token prostředku je spojen s oprávnění v databázi a určuje, zda má uživatel přístup (čtení a zápis, jen pro čtení, nebo žádný přístup) na prostředek aplikace v databázi. Prostředky aplikace obsahují kontejneru, dokumenty, přílohy, uložené procedury, triggery a uživatelem definovanými funkcemi. Token prostředku se pak použije při ověřování zadat nebo odepřít přístup k prostředku.<br><br>Další informace najdete v [zabezpečení přístupu k prostředkům služby Azure Cosmos DB](secure-access-to-data.md).|
|Integrace služby Active directory (RBAC)| Můžete také poskytnout přístup k účtu databáze pomocí řízení přístupu (IAM) na webu Azure Portal, jak je znázorněno na snímku obrazovky pod touto tabulkou. IAM poskytuje řízení přístupu na základě rolí a integruje se službou Active Directory. Integrované role nebo vlastních rolí můžete uživatelům a skupinám jak je znázorněno na následujícím obrázku.|
|Globální replikace|Azure Cosmos DB nabízí globální distribuce na klíč, který umožňuje replikovat data do kterékoli z datových center Azure na celém světě s kliknutím na tlačítko. Globální replikace vám umožní škálovat globálně a zajistit přístup s nízkou latencí k datům po celém světě.<br><br>Globální replikace v kontextu zabezpečení, zajišťuje ochranu dat před selháním v dané oblasti.<br><br>Další informace najdete v části [Globální distribuce dat](distribute-data-globally.md).|
|Regionální převzetí služeb při selhání|Pokud máte replikuje vaše data ve více než jedno datové centrum, Azure Cosmos DB automaticky navyšování vaše operace by měla datovým centrem přejdou do režimu offline. Můžete vytvořit seznam seřazený podle priority převzetí služeb při selhání oblasti použití oblastí, ve kterých se replikuje vaše data. <br><br>Další informace najdete v [regionální převzetí služeb při selhání ve službě Azure Cosmos DB](high-availability.md).|
|Místní replikaci|Dokonce i v rámci jednoho datového centra, Azure Cosmos DB automaticky replikuje data pro zajištění vysoké dostupnosti, dává vám možnost z [úrovně konzistence](consistency-levels.md). To zaručuje 99,99 % [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/cosmos-db) pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999 % dostupnosti pro všechny účty databáze pro více oblastí pro čtení.|
|Automatizované zálohování online|Databáze Azure Cosmos DB se pravidelně Zálohuje a uložená v úložišti georedundant. <br><br>Další informace najdete v [automatické online zálohování a obnovení pomocí služby Azure Cosmos DB](online-backup-and-restore.md).|
|Obnovení odstraněných dat|Automatizované zálohování online je možné obnovit data, možná jste omylem odstranili až ~ 30 dnů po skončení události. <br><br>Další informace najdete v [automatické online zálohování a obnovení pomocí služby Azure Cosmos DB](online-backup-and-restore.md)|
|Ochrana a izolovat citlivá data|Všechna data v oblastech uvedených v co je nového? Nyní se šifrují při nečinnosti.<br><br>Osobní údaje a další důvěrná data můžou být izolované na konkrétní kontejner a čtení a zápis nebo přístup jen pro čtení může být omezen na konkrétní uživatele.|
|Monitorování pro útoky|S použitím [protokolování a aktivit protokolů auditu](logging.md), můžete monitorovat účtu pro běžné a neobvyklé aktivity. Můžete zobrazit, jaké operace provedených s vašimi prostředky, kteří při operaci došlo k chybě, stav operace a mnohem více jak je znázorněno na snímku obrazovky touto tabulkou, který operaci spustil.|
|Reakce na útoky|Jakmile máte kontaktovat podporu Azure o ohlásit možný útok, proces reakcí na incidenty krok 5 je spuštěna. Cíl 5 krocích se má obnovit normální provoz zabezpečení a operace co nejrychleji po zjištění problému a spustit šetření.<br><br>Další informace najdete v [reakce zabezpečení Microsoft Azure v cloudu](https://aka.ms/securityresponsepaper).|
|Geograficky monitorování geografických zón|Azure Cosmos DB zajišťuje řízení dat pro suverénní oblasti (například Germany, Čína, státní správy USA).|
|Chráněné zařízení|Data ve službě Azure Cosmos DB se ukládají na jednotkách SSD v chráněných datových centrech Azure.<br><br>Další informace najdete v [globální síti datových center Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Šifrování protokolu HTTPS a SSL/TLS|Všechny interakce klienta služby Azure Cosmos DB jsou schopné SSL/TLS 1.2. Navíc všechny uvnitř datového centra a různé replikace datového centra je vynucuje SSL/TLS 1.2.|
|Šifrování v klidovém stavu|Všechna data ukládají do služby Azure Cosmos DB se šifrují při nečinnosti. Další informace najdete v [služby Azure Cosmos DB šifrování v klidovém stavu](./database-encryption-at-rest.md)|
|Patched servery|Jako spravovanou databázovou službu Azure Cosmos DB eliminuje potřebu správu a servery, oprava, která má automaticky provede za vás.|
|Účty pro správu s silná hesla|Je těžké si myslíte, že musíme ještě zmiňovat tento požadavek, ale na rozdíl od některých z našich konkurentů, není možné mít účet správce bez hesla ve službě Azure Cosmos DB.<br><br> Ve výchozím nastavení je těšte zabezpečení prostřednictvím protokolu SSL a HMAC tajného kódu na základě ověřování.|
|Zabezpečení a data protection certifikace|Většina až do data seznam certifikací, naleznete v části celkové [dodržování předpisů Azure web](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) a také je [dokumentu dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) s všechny certifikace (vyhledejte Cosmos). Podívejte se na příspěvek k 25. dubna 2018 přesněji zaměřené najdete [Azure #CosmosDB: Zabezpečené, privátní, který vyhovuje](https://azure.microsoft.com/blog/azure-cosmosdb-secure-private-compliant/) obsahující Soc 1/2 typ 2, HITRUST, PCI DSS úrovně 1, ISO 27001, HIPAA, FedRAMP High a mnoha dalších.

Následující snímek obrazovky ukazuje integrace služby Active directory (RBAC) pomocí řízení přístupu (IAM) na webu Azure Portal: ![Řízení přístupu (IAM) na webu Azure Portal – ukázka zabezpečení databáze](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Následující snímek obrazovky ukazuje, jak můžete protokoly auditu protokolování a aktivity k monitorování vašeho účtu: ![Protokoly aktivit pro službu Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Další postup

Další podrobnosti o hlavních klíčů a tokeny prostředků najdete v tématu [zabezpečení přístupu k datům služby Azure Cosmos DB](secure-access-to-data.md).

Další informace o protokolování auditu najdete v tématu [protokolování diagnostiky služby Azure Cosmos DB](logging.md).

Další podrobnosti o certifikacích Microsoft najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).
