---
title: Rotace certifikátu pro Azure SQL Database & spravované instance SQL
description: Seznamte se s nadcházejícími změnami kořenových certifikátů, které budou mít vliv na Azure SQL Database a Azure SQL Managed instance.
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: b0a10744d2b48fa620b48b731144222199f711c7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792527"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Porozumění změnám v kořenové CA změna Azure SQL Database & SQL Managed instance

Azure SQL Database & spravované instance SQL bude měnit kořenový certifikát pro klientskou aplikaci/ovladač, který je povolený s protokolem SSL, který slouží k navázání zabezpečeného připojení TDS. V rámci standardní údržby a osvědčených postupů zabezpečení je [aktuální kořenový certifikát](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) nastaven na vypršení platnosti v 26. října 2020. Tento článek obsahuje podrobné informace o nadcházejících změnách, prostředcích, které budou ovlivněny, a krocích potřebných k tomu, aby vaše aplikace zachovala připojení k vašemu databázovému serveru.

## <a name="what-update-is-going-to-happen"></a>K jaké aktualizaci dojde?

[Fórum prohlížeče certifikační autority (CA)](https://cabforum.org/) nedávno publikované sestavy více certifikátů vydaných dodavateli certifikačních autorit, které nedodržují předpisy.

Podle požadavků na dodržování předpisů v oboru dodavatelé CA začali odvolávat certifikáty certifikační autority pro certifikační autority, které nedodržují předpisy, vyžadují servery k používání certifikátů vydaných kompatibilními certifikačními autoritami a podepisují certifikáty CA z těchto odpovídajících certifikačních autorit Vzhledem k tomu, že Azure SQL Database & spravované instance SQL aktuálně používá jeden z těchto nevyhovujících certifikátů, které klientské aplikace používají k ověření připojení SSL, musíme zajistit, aby byly provedeny vhodné akce (popsané níže), aby se minimalizoval potenciální dopad na vaše servery SQL Azure.

Nový certifikát se použije od 26. října 2020. Pokud při připojování z klienta SQL používáte úplné ověřování certifikátu serveru (TrustServerCertificate = true), musíte zajistit, aby váš klient SQL mohl ověřit nový kořenový certifikát před 26. října 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Návody o tom, jestli by mohla být ovlivněná moje aplikace?

Všechny aplikace, které používají protokol SSL/TLS a ověřují kořenový certifikát, musí aktualizovat kořenový certifikát, aby bylo možné se připojit k Azure SQL Database & spravované instance SQL. 

Pokud aktuálně nepoužíváte protokol SSL/TLS, nemá to žádný vliv na dostupnost vaší aplikace. Můžete ověřit, jestli se klientská aplikace pokouší ověřit kořenový certifikát, a to tak, že prohlíží připojovací řetězec. Pokud je TrustServerCertificate explicitně nastaven na hodnotu true, pak nebudete mít vliv na to.

Pokud klientský ovladač využívá úložiště certifikátů operačního systému jako většinu ovladačů a váš operační systém se pravidelně udržuje, pravděpodobně se vám tato změna neprojeví, protože kořenový certifikát, na který přecházíme, by měl být již k dispozici v úložišti důvěryhodných kořenových certifikátů. Zkontrolujte Baltimore CyberDigiCert GlobalRoot G2 a ověřte, zda je k dispozici.

Pokud klientský ovladač využívá úložiště certifikátů místní soubor, aby nedošlo k přerušení dostupnosti vaší aplikace v důsledku neočekávaného odvolání certifikátů nebo pokud chcete aktualizovat certifikát, který byl odvolán, přečtěte si část co potřebuji k [**zachování připojení**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Co je potřeba udělat, aby se zachovalo připojení

Pokud se chcete vyhnout přerušení dostupnosti vaší aplikace z důvodu neočekávaného odvolání certifikátů nebo aktualizovat certifikát, který byl odvolán, postupujte podle následujících kroků:

*   Stáhněte si kořenovou certifikační autoritu Baltimore CyberTrust Root & DigiCert GlobalRoot z následujících odkazů:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Vygenerujte kombinované úložiště certifikátů certifikační autority s certifikáty **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .

## <a name="what-can-be-the-impact"></a>Jaký může být dopad?
Pokud ověřujete certifikáty serveru, jak je uvedeno zde, může dojít k přerušení dostupnosti vaší aplikace, protože databáze nebude dostupná. V závislosti na vaší aplikaci můžete obdržet nejrůznější chybové zprávy, mezi které patří mimo jiné:
*   Neplatný certifikát nebo odvolaný certifikát
*   Vypršel časový limit připojení
*   Chyba, pokud je k dispozici

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Pokud nepoužívám protokol SSL/TLS, musím pořád aktualizovat kořenovou certifikační autoritu?
Pokud nepoužíváte protokol SSL/TLS, nejsou nutné žádné akce týkající se této změny. Pořád byste měli nastavit plán pro zahájení používání nejnovější verze TLS, protože plánujeme vynucení TLS v blízké budoucnosti.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Co se stane, když kořenový certifikát neaktualizujem před 26. října 2020?
Pokud kořenový certifikát neaktualizujete před 30. listopadu 2020, vaše aplikace, které se připojují přes protokol SSL/TLS, a ověření kořenového certifikátu nebudou moct komunikovat s Azure SQL Database & spravované instance SQL a aplikace budou mít problémy s připojením ke službě Azure SQL Database & SQL Managed instance.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Musím pro tuto změnu naplánovat výpadky údržby?<BR>
Ne. Vzhledem k tomu, že se tato změna dá připojit k serveru jenom na straně klienta, pro tuto změnu není potřeba žádné prostoje údržby.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Co když mi po 26. října 2020 nemůžete získat plánované výpadky této změny?
Vzhledem k tomu, že klienti, kteří se používají pro připojení k serveru, musí aktualizovat informace o certifikátu, jak je popsáno [v části Oprava](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity), v tomto případě nepotřebujeme v tomto případě výpadek serveru.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Když po 30. listopadu 2020 vytvořím nový server, bude to mít dopad na to?
Pro servery vytvořené po 26. října 2020 můžete použít nově vydaný certifikát pro vaše aplikace k připojení pomocí protokolu SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Jak často Microsoft aktualizuje svoje certifikáty nebo jaké jsou zásady vypršení platnosti?
Tyto certifikáty, které používá Azure SQL Database & spravované instance SQL, poskytují důvěryhodné certifikační autority (CA). To znamená, že podpora těchto certifikátů v Azure SQL Database & spravované instance SQL je svázána s podporou těchto certifikátů certifikační autoritou. V takovém případě ale můžou být v těchto předdefinovaných certifikátech nepředvídatelné chyby, které je potřeba vyřešit nejstarší.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>Pokud používám repliky čtení, musím tuto aktualizaci provést jenom na hlavním serveru nebo ve všech replikách pro čtení?
Vzhledem k tomu, že tato aktualizace je změnou na straně klienta, je nutné, aby se při čtení dat ze serveru repliky tyto změny použily i pro tyto klienty. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Máme dotaz na straně serveru, abyste ověřili, jestli se používá SSL?
Vzhledem k tomu, že je tato konfigurace na straně klienta, nejsou informace na straně serveru k dispozici.

### <a name="what-if-i-have-further-questions"></a>Co když mám další dotazy?
Pokud máte plán podpory a potřebujete technickou pomoc, vytvořte žádost o podporu Azure, přečtěte si téma [Vytvoření žádosti o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).