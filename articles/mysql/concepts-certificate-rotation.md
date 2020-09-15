---
title: Otočení certifikátu pro Azure Database for MySQL
description: Seznamte se s nadcházejícími změnami kořenových certifikátů, které budou mít vliv na Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 9c5c4247ab01a571613cad4f33832de152909b11
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527101"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql"></a>Porozumění změnám v kořenové CA se mění Azure Database for MySQL

Azure Database for MySQL bude měnit kořenový certifikát pro klientskou aplikaci/ovladač, který je povolený s protokolem SSL, který se používá pro [připojení k databázovému serveru](concepts-connectivity-architecture.md). Kořenový certifikát, který je aktuálně k dispozici, je v rámci standardní údržby a osvědčených postupů pro zabezpečení nastaven na vypršení platnosti 26. října 2020 (10/26/2020). Tento článek obsahuje podrobné informace o nadcházejících změnách, prostředcích, které budou ovlivněny, a krocích potřebných k tomu, aby vaše aplikace zachovala připojení k vašemu databázovému serveru.

## <a name="what-update-is-going-to-happen"></a>K jaké aktualizaci dojde?

V některých případech aplikace používají k zabezpečenému připojení soubor místního certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (CA). Zákazníci v současné době můžou k připojení k Azure Database for MySQL serveru použít jenom předdefinovaný certifikát, který najdete [tady](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Fórum pro [prohlížeč certifikační autority (CA)](https://cabforum.org/)ale   nedávno publikované sestavy více certifikátů vydaných dodavateli CA nedodržují předpisy.

Podle požadavků na dodržování předpisů v oboru dodavatelé CA začali odvolávat certifikáty certifikační autority pro certifikační autority, které nedodržují předpisy, vyžadují servery k používání certifikátů vydaných kompatibilními certifikačními autoritami a podepisují certifikáty CA z těchto odpovídajících certifikačních autorit Vzhledem k tomu, že Azure Database for MySQL aktuálně používá jeden z těchto nekompatibilních certifikátů, které klientské aplikace používají k ověření připojení SSL, musíme zajistit, aby byly provedeny vhodné akce (popsané níže), aby se minimalizoval potenciální dopad na servery MySQL.

Nový certifikát se použije od 26. října 2020 (10/26/2020). Pokud při připojování z klienta MySQL použijete ověření certifikační autority nebo úplné ověření certifikátu serveru (sslmode = ověřit-CA nebo sslmode = ověřit-plná), musíte aktualizovat konfiguraci aplikace před 26. října 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Návody informace o tom, jestli se má tato databáze týkat?

Všechny aplikace, které používají protokol SSL/TLS a ověřují kořenový certifikát, musí aktualizovat kořenový certifikát, aby se mohl připojit k Azure Database for MySQL. Pokud aktuálně nepoužíváte protokol SSL/TLS, nemá to žádný vliv na dostupnost vaší aplikace. Můžete ověřit, jestli se klientská aplikace pokouší použít režim SSL s předem definovanou důvěryhodnou certifikační autoritou ( [CA).](concepts-ssl-connection-security.md#ssl-default-settings)

Aby nedošlo k přerušení dostupnosti vaší aplikace v důsledku neočekávaného odvolání certifikátů nebo pokud chcete aktualizovat certifikát, který byl odvolán, přečtěte si část "co potřebuji ke [**správě připojení"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Co je potřeba udělat, aby se zachovalo připojení

Pokud se chcete vyhnout přerušení dostupnosti vaší aplikace z důvodu neočekávaného odvolání certifikátů nebo aktualizovat certifikát, který byl odvolán, postupujte podle následujících kroků:

*   Stáhněte si kořenovou certifikační autoritu BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 z následujících odkazů:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Vygenerujte kombinované úložiště certifikátů certifikační autority s certifikáty **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .
    *   Pro uživatele Java (MySQL Connector/J) spusťte:

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Pak původní soubor úložiště klíčů nahraďte nově vygenerovaným:
        *   System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. NET. SSL. trustStorePassword"; "Password");

    *   Pro uživatele .NET (MySQL Connector/NET, MySQLConnector) zajistěte, aby **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existovaly v úložišti certifikátů Windows, důvěryhodných kořenových certifikačních autorit. Pokud nějaké certifikáty neexistují, importujte chybějící certifikát.

        ![Azure Database for MySQL certifikát .NET](media/overview/netconnecter-cert.png)

    *   Pro uživatele rozhraní .NET na platformě Linux pomocí SSL_CERT_DIR zajistěte, aby **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existovaly v adresáři označeném SSL_CERT_DIR. Pokud nějaké certifikáty neexistují, vytvořte soubor s chybějícím certifikátem.

    *   Pro ostatní uživatele (MySQL Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) můžete sloučit dva soubory certifikátů certifikační autority, jako je tento formát níže.</b>

        </br>-----ZAČÍT S CERTIFIKÁTEM-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----UKONČIT-----CERTIFIKÁTU
 </br>-----ZAČÍT S CERTIFIKÁTEM-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----UKONČIT-----CERTIFIKÁTU

*   Nahraďte soubor PEM původní kořenové certifikační autority souborem kombinované kořenové certifikační autority a restartujte aplikaci nebo klienta.
*   Po nasazení nového certifikátu na straně serveru můžete v budoucnu změnit soubor PEM certifikační autority na DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact"></a>Jaký může být dopad?
Pokud používáte certifikát vydaný Azure Database for MySQL, jak je uvedeno zde, může dojít k přerušení dostupnosti vaší aplikace, protože databáze nebude dostupná. V závislosti na vaší aplikaci můžete obdržet nejrůznější chybové zprávy, mezi které patří mimo jiné:
*   Neplatný certifikát nebo odvolaný certifikát
*   Vypršel časový limit připojení
*   Chyba, pokud je k dispozici

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Pokud nepoužíváte protokol SSL/TLS, musím pořád aktualizovat kořenovou certifikační autoritu?
Pokud nepoužíváte protokol SSL/TLS, nejsou vyžadovány žádné akce. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Pokud používám protokol SSL/TLS, je nutné restartovat server databáze a aktualizovat tak kořenovou certifikační autoritu?
Ne, nemusíte restartovat databázový server, abyste mohli začít používat nový certifikát. Tento kořenový certifikát je změnou na straně klienta a příchozí připojení klientů musí použít nový certifikát, aby se mohl připojit k databázovému serveru.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. co se stane, když kořenový certifikát neaktualizujem před 26. října 2020 (10/26/2020)?
Pokud kořenový certifikát neaktualizujete před 26. října 2020, vaše aplikace, které se připojují přes protokol SSL/TLS, a ověření kořenového certifikátu nebudou moct komunikovat s databázovým serverem MySQL a aplikace bude mít problémy s připojením k databázovému serveru MySQL.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. Musím pro tuto změnu naplánovat výpadek údržby?<BR>
No. Vzhledem k tomu, že se tato změna nachází pouze na straně klienta pro připojení k databázovému serveru, pro tuto změnu není potřeba žádné prostoje údržby.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. co když mi pro tuto změnu nezíská plánované výpadky před 26. října 2020 (10/26/2020)?
Vzhledem k tomu, že klienti, kteří se používají pro připojení k serveru, musí aktualizovat informace o certifikátu, jak je popsáno [v části Oprava](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), v tomto případě nepotřebujeme v tomto případě výpadek serveru.

###  <a name="6-if-i-create-a-new-server-after-october-26-2020-will-i-be-impacted"></a>6. když po 26. říjnu 2020 vytvořím nový server, bude to mít dopad na to?
Pro servery vytvořené po 26. října 2020 (10/26/2020) můžete použít nově vydaný certifikát pro vaše aplikace k připojení pomocí protokolu SSL.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. jak často Microsoft aktualizuje svoje certifikáty nebo jaké jsou zásady vypršení platnosti?
Certifikáty používané službou Azure Database for MySQL poskytují důvěryhodné certifikační autority (CA). Proto je podpora těchto certifikátů na Azure Database for MySQL vázaná na podporu těchto certifikátů certifikační autoritou. V takovém případě ale můžou být v těchto předdefinovaných certifikátech nepředvídatelné chyby, které je potřeba vyřešit nejstarší.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. Pokud používám repliky pro čtení, musím tuto aktualizaci provést jenom na hlavním serveru nebo v replikách pro čtení?
Vzhledem k tomu, že tato aktualizace je změnou na straně klienta, je nutné, aby se při čtení dat ze serveru repliky tyto změny použily i pro tyto klienty. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. máme dotaz na straně serveru, abyste ověřili, jestli se používá SSL?
Chcete-li ověřit, zda používáte připojení SSL pro připojení k serveru, postupujte podle tématu [ověřování SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

### <a name="10-what-if-i-have-further-questions"></a>10. co když mám další dotazy?
Pokud máte nějaké otázky, Získejte odpovědi od expertů komunity v [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Pokud máte plán podpory a potřebujete technickou pomoc, [kontaktujte nás](mailto:AzureDatabaseforMySQL@service.microsoft.com) .