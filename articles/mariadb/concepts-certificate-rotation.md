---
title: Otočení certifikátu pro Azure Database for MariaDB
description: Seznamte se s nadcházejícími změnami kořenových certifikátů, které budou mít vliv na Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 02c0ecfc24b65afd46d75464b5411cfd5cf61857
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591527"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Porozumění změnám v kořenové CA se mění Azure Database for MariaDB

Azure Database for MariaDB změní kořenový certifikát pro klientskou aplikaci/ovladač, který je povolený s protokolem SSL, použijte pro [připojení k databázovému serveru](concepts-connectivity-architecture.md). Aktuálně dostupný kořenový certifikát má 15. února 2021 (02/15/2021) jako součást standardní údržby a osvědčených postupů zabezpečení. Tento článek obsahuje podrobné informace o nadcházejících změnách, prostředcích, které budou ovlivněny, a krocích potřebných k tomu, aby vaše aplikace zachovala připojení k vašemu databázovému serveru.

>[!NOTE]
> Na základě zpětné vazby od zákazníků jsme rozšířili vyřazení kořenových certifikátů pro naši stávající kořenovou certifikační autoritu Baltimore od října 26, 2020 do 15. února 2021. Doufáme, že toto rozšíření poskytuje dostatek času vedoucího k tomu, aby naši uživatelé mohli implementovat změny klienta, pokud budou ovlivněny.

## <a name="what-update-is-going-to-happen"></a>K jaké aktualizaci dojde?

V některých případech aplikace používají k zabezpečenému připojení soubor místního certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (CA). Zákazníci v současné době můžou k připojení k Azure Database for MariaDB serveru použít jenom předdefinovaný certifikát, který najdete [tady](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Fórum pro [prohlížeč certifikační autority (CA)](https://cabforum.org/)ale   nedávno publikované sestavy více certifikátů vydaných dodavateli CA nedodržují předpisy.

Podle požadavků na dodržování předpisů v oboru dodavatelé CA začali odvolávat certifikáty certifikační autority pro certifikační autority, které nedodržují předpisy, vyžadují servery k používání certifikátů vydaných kompatibilními certifikačními autoritami a podepisují certifikáty CA z těchto odpovídajících certifikačních autorit Vzhledem k tomu, že Azure Database for MariaDB aktuálně používá jeden z těchto nekompatibilních certifikátů, které klientské aplikace používají k ověření připojení SSL, musíme zajistit, aby byly provedeny vhodné akce (popsané níže), aby se minimalizoval potenciální dopad na servery MariaDB.


Nový certifikát se použije od 15. února 2021 (02/15/2021). Pokud při připojování z klienta MySQL používáte ověření certifikační autority nebo úplné ověřování certifikátu serveru (sslmode = ověřit-CA nebo sslmode = ověřit-plná), musíte aktualizovat konfiguraci aplikace před 15. února 2021 (02/15/2021).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Návody informace o tom, jestli se má tato databáze týkat?

Všechny aplikace, které používají protokol SSL/TLS a ověřují kořenový certifikát, musí aktualizovat kořenový certifikát. Můžete zjistit, jestli vaše připojení ověřují kořenový certifikát, a to tak, že zkontrolujete připojovací řetězec.
-    Pokud váš připojovací řetězec obsahuje `sslmode=verify-ca` nebo `sslmode=verify-identity` , musíte aktualizovat certifikát.
-    Pokud váš připojovací řetězec obsahuje `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` nebo `sslmode=require` , nemusíte aktualizovat certifikáty. 
-    Pokud v připojovacím řetězci neurčíte sslmode, nemusíte aktualizovat certifikáty.

Pokud používáte klienta, který vyabstrakce připojovací řetězec, přečtěte si dokumentaci klienta, abyste zjistili, zda ověřuje certifikáty.
Pokud chcete pochopit Azure Database for MariaDB sslmode, přečtěte si téma [popisy režimu SSL](concepts-ssl-connection-security.md#default-settings).

Aby nedošlo k přerušení dostupnosti vaší aplikace v důsledku neočekávaného odvolání certifikátů nebo pokud chcete aktualizovat certifikát, který byl odvolán, přečtěte si část "co potřebuji ke [**správě připojení"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Co je potřeba udělat, aby se zachovalo připojení

Pokud se chcete vyhnout přerušení dostupnosti vaší aplikace z důvodu neočekávaného odvolání certifikátů nebo aktualizovat certifikát, který byl odvolán, postupujte podle následujících kroků. Nápad je vytvořit nový soubor *. pem* , který kombinuje aktuální certifikát a nové a v průběhu ověřování certifikátu SSL, jakmile budou použity povolené hodnoty. Přečtěte si následující postup:

*   Stáhněte si  &  certifikační autoritu BaltimoreCyberTrustRoot **DigiCertGlobalRootG2** z následujících odkazů:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Vygenerujte kombinované úložiště certifikátů certifikační autority s certifikáty **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .
    *   Pro uživatele Java (MariaDB Connector/J) spusťte:

          ```console
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Pak původní soubor úložiště klíčů nahraďte nově vygenerovaným:
        *   System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. NET. SSL. trustStorePassword"; "Password");
    *   Pro uživatele .NET (MariaDB Connector/NET, MariaDBConnector) se ujistěte, že **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existují v úložišti certifikátů Windows, důvěryhodných kořenových certifikačních autorit. Pokud nějaké certifikáty neexistují, importujte chybějící certifikát.

        ![Azure Database for MariaDB certifikát .NET](media/overview/netconnecter-cert.png)

    *   Pro uživatele rozhraní .NET na platformě Linux pomocí SSL_CERT_DIR zajistěte, aby **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existovaly v adresáři označeném SSL_CERT_DIR. Pokud nějaké certifikáty neexistují, vytvořte soubor s chybějícím certifikátem.

    *   Pro ostatní uživatele (MariaDB Client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) můžete sloučit dva soubory certifikátů certifikační autority, jako je tento formát níže.</b>

        </br>-----ZAČÍT S CERTIFIKÁTEM-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----UKONČIT-----CERTIFIKÁTU
 </br>-----ZAČÍT S CERTIFIKÁTEM-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----UKONČIT-----CERTIFIKÁTU

*   Nahraďte soubor PEM původní kořenové certifikační autority souborem kombinované kořenové certifikační autority a restartujte aplikaci nebo klienta.
*    Po nasazení nového certifikátu na straně serveru můžete v budoucnu změnit soubor PEM certifikační autority na DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Co může být důsledkem neaktualizace certifikátu?
Pokud používáte certifikát vydaný Azure Database for MariaDB, jak je uvedeno zde, může dojít k přerušení dostupnosti vaší aplikace, protože databáze nebude dostupná. V závislosti na vaší aplikaci můžete obdržet nejrůznější chybové zprávy, mezi které patří mimo jiné:
*    Neplatný certifikát nebo odvolaný certifikát
*    Vypršel časový limit připojení

> [!NOTE]
> **Baltimore certifikát** prosím neodstraňujte ani neměňte, dokud se neprovede změna certifikátu. Až se změna dokončí, pošle se vám komunikace, po jejímž uplynutí se certifikát Baltimore bezpečně vyřadí. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Pokud nepoužíváte protokol SSL/TLS, musím pořád aktualizovat kořenovou certifikační autoritu?
Pokud nepoužíváte protokol SSL/TLS, nejsou vyžadovány žádné akce. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Pokud používám protokol SSL/TLS, je nutné restartovat server databáze a aktualizovat tak kořenovou certifikační autoritu?
Ne, nemusíte restartovat databázový server, abyste mohli začít používat nový certifikát. Aktualizace certifikátu je změna na straně klienta a příchozí připojení klientů musí použít nový certifikát k zajištění toho, aby se mohli připojit k databázovému serveru.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-february-15-2021-02152021"></a>3. k tomu, co se stane, když neaktualizujem kořenový certifikát před 15. února 2021 (02/15/2021)?
Pokud kořenový certifikát neaktualizujete před 15. února 2021 (02/15/2021), vaše aplikace, které se připojují přes protokol SSL/TLS a ověřují pro kořenový certifikát, nebudou moct komunikovat s databázovým serverem MariaDB a aplikace bude mít problémy s připojením k vašemu databázovému serveru MariaDB.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. Jaký je dopad při použití App Service s Azure Database for MariaDB?
V případě Azure App Services můžeme mít Azure Database for MariaDB k dispozici dva možné scénáře a závisí na tom, jak používáte protokol SSL s vaší aplikací.
*   Tento nový certifikát se přidal do App Service na úrovni platformy. Pokud používáte certifikáty SSL obsažené na platformě App Service v aplikaci, není nutné provádět žádnou akci.
*   Pokud explicitně zadáte cestu k souboru certifikátu SSL v kódu, budete muset stáhnout nový certifikát a aktualizovat kód, aby používal nový certifikát. Dobrým příkladem tohoto scénáře je použití vlastních kontejnerů v App Service jako sdílené v [dokumentaci k App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. Jaký je dopad použití služeb Azure Kubernetes Services (AKS) s Azure Database for MariaDB?
Pokud se pokoušíte připojit k Azure Database for MariaDB pomocí služeb Azure Kubernetes Services (AKS), bude se podobat přístupu z hostitelského prostředí s vyhrazenými zákazníky. Postup najdete [tady](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. Jaký je dopad použití Azure Data Factory pro připojení k Azure Database for MariaDB?
Pro konektor používající Azure Integration Runtime konektor využívá certifikáty v úložišti certifikátů Windows v prostředí hostovaném v Azure. Tyto certifikáty jsou již kompatibilní s nově použitými certifikáty, a proto není nutné provádět žádnou akci.

Pro konektor používající Integration Runtime v místním prostředí, kde explicitně zadáte cestu k souboru certifikátu SSL v připojovacím řetězci, budete muset stáhnout [nový certifikát](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a aktualizovat připojovací řetězec tak, aby ho používal.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. pro tuto změnu potřebuji naplánovat výpadek údržby databázového serveru?
No. Vzhledem k tomu, že se tato změna provádí jenom na straně klienta, aby se mohla připojit k databázovému serveru, není pro tuto změnu pro databázový server potřeba žádné prostoje údržby.

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8. co když mi nejde získat plánované výpadky této změny před 15. února 2021 (02/15/2021)?
Vzhledem k tomu, že klienti, kteří se používají pro připojení k serveru, musí aktualizovat informace o certifikátu, jak je popsáno [v části Oprava](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), v tomto případě nepotřebujeme v tomto případě výpadek serveru.

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9. když po 15. únoru 2021 (02/15/2021) vytvořím nový server, bude to mít vliv?
Pro servery vytvořené po 15. únoru 2021 (02/15/2021) můžete použít nově vydaný certifikát pro vaše aplikace k připojení pomocí protokolu SSL.

###    <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. jak často Microsoft aktualizuje svoje certifikáty nebo jaké jsou zásady vypršení platnosti?
Certifikáty používané službou Azure Database for MariaDB poskytují důvěryhodné certifikační autority (CA). Proto je podpora těchto certifikátů na Azure Database for MariaDB vázaná na podporu těchto certifikátů certifikační autoritou. V takovém případě ale můžou být v těchto předdefinovaných certifikátech nepředvídatelné chyby, které je potřeba vyřešit nejstarší.

###    <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11. Pokud používám repliky pro čtení, musím tuto aktualizaci provést pouze na zdrojovém serveru nebo v replikách pro čtení?
Vzhledem k tomu, že se jedná o změnu na straně klienta, pokud klient používá ke čtení dat ze serveru repliky, budete muset změny použít i pro tyto klienty.

###    <a name="12-if-i-am-using-data-in-replication-do-i-need-to-perform-any-action"></a>12. Pokud používám replikaci dat, potřebuji provést jakoukoli akci?

Pokud pro připojení k Azure Database for MySQL používáte [replikaci dat](concepts-data-in-replication.md) , je potřeba vzít v úvahu dvě věci:

> [!NOTE]
>  Tento článek obsahuje odkazy na podřízený termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.
*   Pokud je replikace dat z virtuálního počítače (Prem nebo virtuálního počítače Azure) na Azure Database for MySQL, musíte ověřit, jestli se k vytvoření repliky používá protokol SSL. Spusťte příkaz **Zobrazit stav podřízeného** a ověřte následující nastavení. 

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Pokud se zobrazí certifikát pro CA_file, SSL_Cert a SSL_Key, budete muset soubor aktualizovat přidáním [nového certifikátu](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem).

*   Pokud je replikace dat mezi dvěma Azure Database for MySQL, pak budete muset resetovat repliku spuštěním **MySQL.az_replication_change_master volání** a zadat nový duální kořenový certifikát jako poslední parametr [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication).

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13. máme dotaz na straně serveru, abyste ověřili, jestli se používá SSL?
Chcete-li ověřit, zda používáte připojení SSL pro připojení k serveru, postupujte podle tématu [ověřování SSL](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14. je vyžadována akce, pokud již mám v souboru certifikátu DigiCertGlobalRootG2?
No. Pokud soubor certifikátu již má **DigiCertGlobalRootG2**, není nutná žádná akce.

###    <a name="15-what-if-i-have-further-questions"></a>15. co když mám další dotazy?
Pokud máte nějaké otázky, Získejte odpovědi od expertů komunity v [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). Pokud máte plán podpory a potřebujete technickou pomoc, [kontaktujte nás](mailto:AzureDatabaseformariadb@service.microsoft.com).
