---
title: Rotace certifikátu pro Azure Database for PostgreSQL jeden server
description: Seznamte se s nadcházejícími změnami kořenových certifikátů, které budou mít vliv na Azure Database for PostgreSQL jeden server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: c9bc6e3822ac6c014b9ff00e9cd81bbe707628fe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736062"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Porozumění změnám v kořenové CA změna pro Azure Database for PostgreSQL jeden server

Azure Database for PostgreSQL jeden server úspěšně dokončil změnu kořenového certifikátu od **15. února 2021 (02/15/2021)** jako součást standardní údržby a osvědčených postupů zabezpečení. Tento článek obsahuje podrobné informace o změnách, ovlivněných zdrojích a krocích potřebných k tomu, aby vaše aplikace zachovala připojení k databázovému serveru.

## <a name="why-root-certificate-update-is-required"></a>Proč se vyžaduje aktualizace kořenového certifikátu?

Uživatelé Azure Database for PostgreSQL můžou k připojení k serveru PostgreSQL použít jenom předdefinovaný certifikát, který najdete [tady](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Fórum pro [prohlížeč certifikační autority (CA)](https://cabforum.org/)ale   nedávno publikované sestavy více certifikátů vydaných dodavateli CA nedodržují předpisy.

Podle požadavků na dodržování předpisů v oboru dodavatelé CA začali odvolávat certifikáty certifikační autority pro certifikační autority, které nedodržují předpisy, vyžadují servery k používání certifikátů vydaných kompatibilními certifikačními autoritami a podepisují certifikáty CA z těchto odpovídajících certifikačních autorit Vzhledem k tomu, že Azure Database for MySQL používal jeden z těchto nekompatibilních certifikátů, musíme tento certifikát otočit do odpovídající verze, aby se minimalizovala potenciální hrozba pro vaše servery MySQL.

Nový certifikát se zavede a začne od 15. února 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Jakou změnu provedl 15. února 2021 (02/15/2021)?

15. února 2021 byl [kořenový certifikát BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) nahrazený **odpovídající verzí** stejného [kořenového certifikátu BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , aby stávající zákazníci nemuseli nic měnit a neovlivnili by jejich připojení k serveru. Během této změny nebyl [kořenový certifikát BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nahrazený** [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a tato změna je odložena, aby zákazníci mohli provést změnu.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Musím v mém klientovi dělat změny, aby se zachovalo připojení?

Na straně klienta není vyžadována žádná změna. Pokud jste postupovali podle našeho předchozího doporučení níže, budete se moci nadále připojit, dokud **neodeberete certifikát BaltimoreCyberTrustRoot** ze kombinovaného certifikátu certifikační autority. **Doporučujeme, abyste neodebrali BaltimoreCyberTrustRoot ze svého kombinovaného certifikátu certifikační autority, dokud nebudete moci další oznámení zachovat připojení.**

### <a name="previous-recommendation"></a>Předchozí doporučení

*   Stáhněte si kořenovou certifikační autoritu BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 z následujících odkazů:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Vygenerujte kombinované úložiště certifikátů certifikační autority s certifikáty **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .
    *   Pro uživatele Java (PostgreSQL JDBC), kteří používají DefaultJavaSSLFactory, spusťte:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Pak původní soubor úložiště klíčů nahraďte nově vygenerovaným:
        *   System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. NET. SSL. trustStorePassword"; "Password");
        
    *   Pro uživatele .NET (Npgsql) ve Windows se ujistěte, že **kořenový adresář Baltimore CyberTrust** a **DigiCert globální root G2** existují v úložišti certifikátů Windows, důvěryhodných kořenových certifikačních autorit. Pokud nějaké certifikáty neexistují, importujte chybějící certifikát.

        ![Azure Database for PostgreSQL certifikát .NET](media/overview/netconnecter-cert.png)

    *   Pro uživatele .NET (Npgsql) na platformě Linux pomocí SSL_CERT_DIR zajistěte, aby **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existovaly v adresáři označeném SSL_CERT_DIR. Pokud nějaké certifikáty neexistují, vytvořte soubor s chybějícím certifikátem.

    *   Pro ostatní uživatele klienta PostgreSQL můžete sloučit dva soubory certifikátů certifikační autority, jako je tento formát níže.

        </br>-----ZAČÍT S CERTIFIKÁTEM-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----UKONČIT-----CERTIFIKÁTU
 </br>-----ZAČÍT S CERTIFIKÁTEM-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----UKONČIT-----CERTIFIKÁTU

*   Nahraďte soubor PEM původní kořenové certifikační autority souborem kombinované kořenové certifikační autority a restartujte aplikaci nebo klienta.
*    Po nasazení nového certifikátu na straně serveru můžete v budoucnu změnit soubor PEM certifikační autority na DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> **Baltimore certifikát** prosím neodstraňujte ani neměňte, dokud se neprovede změna certifikátu. Až se změna dokončí, pošle se vám komunikace, po jejímž uplynutí se certifikát Baltimore bezpečně vyřadí. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Proč se BaltimoreCyberTrustRoot certifikát nenahradil na DigiCertGlobalRootG2 během této změny od 15. února 2021?

Vyhodnotili jsme připravenost zákazníků na tuto změnu a realizované množství zákazníků si vyhledalo další dobu realizace pro správu této změny. V zájmu zajištění většího počtu potenciálních zákazníků zákazníkům pro připravenost, rozhodli jsme se odložit změnu certifikátu na DigiCertGlobalRootG2 nejméně na rok, který zákazníkům a koncovým uživatelům poskytne dostatek času. 

Naše doporučení pro uživatele: pomocí výše uvedených kroků vytvořte Kombinovaný certifikát a připojte se k serveru, ale neodstraňujte BaltimoreCyberTrustRoot certifikát, dokud pošleme oznámení, že ho odebrat. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Co když jsme odebrali certifikát BaltimoreCyberTrustRoot?

Při připojování k serveru Azure Database for PostgreSQL začnete s připojením k chybám. Pro zachování připojení budete muset [nakonfigurovat SSL](howto-configure-ssl.md) pomocí certifikátu [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) .


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Pokud nepoužíváte protokol SSL/TLS, musím pořád aktualizovat kořenovou certifikační autoritu?
Pokud nepoužíváte protokol SSL/TLS, nejsou vyžadovány žádné akce. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Pokud používám protokol SSL/TLS, je nutné restartovat server databáze a aktualizovat tak kořenovou certifikační autoritu?
Ne, nemusíte restartovat databázový server, abyste mohli začít používat nový certifikát. Jedná se o změnu na straně klienta a příchozí připojení klientů musí použít nový certifikát k zajištění toho, aby se mohli připojit k databázovému serveru.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Návody vědět, jestli používám protokol SSL/TLS s ověřováním kořenových certifikátů?

Můžete zjistit, jestli vaše připojení ověřují kořenový certifikát, a to tak, že zkontrolujete připojovací řetězec.
-    Pokud váš připojovací řetězec obsahuje `sslmode=verify-ca` nebo `sslmode=verify-full` , musíte aktualizovat certifikát.
-    Pokud váš připojovací řetězec obsahuje `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` nebo `sslmode=require` , nemusíte aktualizovat certifikáty. 
-    Pokud v připojovacím řetězci neurčíte sslmode, nemusíte aktualizovat certifikáty.

Pokud používáte klienta, který vyabstrakce připojovací řetězec, přečtěte si dokumentaci klienta, abyste zjistili, zda ověřuje certifikáty. Informace o PostgreSQL sslmode naleznete v [popisu režimu SSL](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) v dokumentaci PostgreSQL.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. Jaký je dopad při použití App Service s Azure Database for PostgreSQL?
V případě Azure App Services můžeme mít Azure Database for PostgreSQL k dispozici dva možné scénáře a závisí na tom, jak používáte protokol SSL s vaší aplikací.
*   Tento nový certifikát se přidal do App Service na úrovni platformy. Pokud používáte certifikáty SSL obsažené na platformě App Service v aplikaci, není nutné provádět žádnou akci.
*   Pokud explicitně zadáte cestu k souboru certifikátu SSL v kódu, budete muset stáhnout nový certifikát a aktualizovat kód, aby používal nový certifikát. Dobrým příkladem tohoto scénáře je použití vlastních kontejnerů v App Service jako sdílené v [dokumentaci k App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. Jaký je dopad použití služeb Azure Kubernetes Services (AKS) s Azure Database for PostgreSQL?
Pokud se pokoušíte připojit k Azure Database for PostgreSQL pomocí služeb Azure Kubernetes Services (AKS), bude se podobat přístupu z hostitelského prostředí s vyhrazenými zákazníky. Postup najdete [tady](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. Jaký je dopad použití Azure Data Factory pro připojení k Azure Database for PostgreSQL?
Pro konektor používající Azure Integration Runtime konektor využívá certifikáty v úložišti certifikátů Windows v prostředí hostovaném v Azure. Tyto certifikáty jsou již kompatibilní s nově použitými certifikáty, a proto není nutné provádět žádnou akci.

Pro konektor používající Integration Runtime v místním prostředí, kde explicitně zadáte cestu k souboru certifikátu SSL v připojovacím řetězci, budete muset stáhnout [nový certifikát](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a aktualizovat připojovací řetězec tak, aby ho používal.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. pro tuto změnu potřebuji naplánovat výpadek údržby databázového serveru?
No. Vzhledem k tomu, že se tato změna provádí jenom na straně klienta, aby se mohla připojit k databázovému serveru, není pro tuto změnu pro databázový server potřeba žádné prostoje údržby.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. když po 15. únoru 2021 (02/15/2021) vytvořím nový server, bude to mít dopad na to?
Pro servery vytvořené po 15. únoru 2021 (02/15/2021) budete nadále používat [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) pro vaše aplikace pro připojení pomocí protokolu SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. jak často Microsoft aktualizuje svoje certifikáty nebo jaké jsou zásady vypršení platnosti?
Certifikáty používané službou Azure Database for PostgreSQL poskytují důvěryhodné certifikační autority (CA). Proto je podpora těchto certifikátů svázána s podporou těchto certifikátů certifikační autoritou. Platnost certifikátu [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) je naplánována na hodnotu 2025, takže společnost Microsoft bude muset před vypršením platnosti provést změnu certifikátu. V případě, že jsou v těchto předdefinovaných certifikátech nepředvídatelné chyby, společnost Microsoft bude muset provést rotaci certifikátu v nejbližším případě změny provedené 15. února 2021, aby byla služba zabezpečená a v souladu s předpisy.

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. Pokud používám repliky čtení, musím tuto aktualizaci provést pouze na primárním serveru nebo v replikách pro čtení?
Vzhledem k tomu, že se jedná o změnu na straně klienta, pokud klient používá ke čtení dat ze serveru repliky, budete muset změny použít i pro tyto klienty. 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. máme dotaz na straně serveru, abyste ověřili, jestli se používá SSL?
Chcete-li ověřit, zda používáte připojení SSL pro připojení k serveru, postupujte podle tématu [ověřování SSL](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. je vyžadována akce, pokud již mám v souboru certifikátu DigiCertGlobalRootG2?
No. Pokud soubor certifikátu již má **DigiCertGlobalRootG2**, není nutná žádná akce.

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. co když používáte Docker image PgBouncerového vozíku od Microsoftu?
Nová Image Docker, která podporuje [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) i [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) , je publikovaná níže [(nejnovější](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) značka). Tento nový obrázek si můžete vyžádat, abyste se vyhnuli jakémukoli přerušení připojení od 15. února 2021. 

### <a name="14-what-if-i-have-further-questions"></a>14. co když mám další dotazy?
Pokud máte nějaké otázky, Získejte odpovědi od expertů komunity v [Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Pokud máte plán podpory a potřebujete technickou pomoc, [kontaktujte nás](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com) .
