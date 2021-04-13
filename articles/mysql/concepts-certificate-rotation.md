---
title: Otočení certifikátu pro Azure Database for MySQL
description: Seznamte se s nadcházejícími změnami kořenových certifikátů, které budou mít vliv na Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b218a9481fdd5970fd6fc8fa6a1d071161e5b58
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313360"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Porozumění změnám v kořenové CA změna pro Azure Database for MySQL jeden server

Azure Database for MySQL jeden server úspěšně dokončil změnu kořenového certifikátu od **15. února 2021 (02/15/2021)** jako součást standardní údržby a osvědčených postupů zabezpečení. Tento článek obsahuje podrobné informace o změnách, ovlivněných zdrojích a krocích potřebných k tomu, aby vaše aplikace zachovala připojení k databázovému serveru.

> [!NOTE]
> Tento článek se týká jenom [Azure Database for MySQL – jenom pro jeden server](single-server-overview.md) . Pro [Azure Database for MySQL-flexibilní Server](flexible-server/overview.md)je certifikát potřebný pro komunikaci přes SSL [DigiCert globální kořenovou certifikační autoritou](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) .
>
> Tento článek obsahuje odkazy na _podřízený_ termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Proč je vyžadována aktualizace kořenového certifikátu?

Azure Database for MySQL uživatelé můžou k připojení k serveru MySQL použít jenom předdefinovaný certifikát, který najdete [tady](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Fórum pro [prohlížeč certifikační autority (CA)](https://cabforum.org/)ale   nedávno publikované sestavy více certifikátů vydaných dodavateli CA nedodržují předpisy.

Výrobci CA zahájili odvolání certifikátů certifikační autority pro certifikační autority, které nedodržují předpisy, a vyžadují, aby servery používaly certifikáty vydané kompatibilními certifikačními autoritami a certifikáty certifikační autority od těchto odpovídajících certifikačních autorit. Vzhledem k tomu, že Azure Database for MySQL používal jeden z těchto nekompatibilních certifikátů, musíme tento certifikát otočit do odpovídající verze, aby se minimalizovala potenciální hrozba pro vaše servery MySQL.

Nový certifikát se zavede a nabývá od 15. února 2021 (02/15/2021).

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Jakou změnu provedl 15. února 2021 (02/15/2021)?

15. února 2021 byl [kořenový certifikát BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) nahrazený **odpovídající verzí** stejného [kořenového certifikátu BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , aby stávající zákazníci nemuseli nic měnit a neovlivnili by jejich připojení k serveru. Během této změny nebyl [kořenový certifikát BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nahrazený** [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a tato změna je odložena, aby zákazníci mohli provést změnu.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Musím v mém klientovi dělat změny, aby se zachovalo připojení?

Na straně klienta není vyžadována žádná změna. Pokud jste postupovali podle našeho předchozího doporučení níže, můžete se i nadále připojovat, dokud **neodeberete certifikát BaltimoreCyberTrustRoot** ze kombinovaného certifikátu certifikační autority. **Aby se zachovalo připojení, doporučujeme, abyste zachovali BaltimoreCyberTrustRoot v kombinovaném certifikátu certifikační autority až do dalšího upozornění.**

###### <a name="previous-recommendation"></a>Předchozí doporučení

Chcete-li se vyhnout přerušení dostupnosti vaší aplikace v důsledku neočekávaného odvolání certifikátů nebo aktualizovat odvolaný certifikát, použijte následující postup. Nápad je vytvořit nový soubor *. pem* , který kombinuje aktuální certifikát a nové a během ověřování certifikátu SSL se použije jedna z povolených hodnot. Přečtěte si následující postup:

1. Stáhněte si kořenovou certifikační autoritu BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 z následujících odkazů:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Vygenerujte kombinované úložiště certifikátů certifikační autority s certifikáty **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .

    * Pro uživatele Java (MySQL Connector/J) spusťte:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Pak původní soubor úložiště klíčů nahraďte nově vygenerovaným:

      * System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file");
      * System. setProperty ("javax. NET. SSL. trustStorePassword"; "Password");

    * Pro uživatele .NET (MySQL Connector/NET, MySQLConnector) zajistěte, aby **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existovaly v úložišti certifikátů Windows, důvěryhodných kořenových certifikačních autorit. Pokud nějaké certifikáty neexistují, importujte chybějící certifikát.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL diagram certifikátů .NET":::

    * Pro uživatele rozhraní .NET na platformě Linux pomocí SSL_CERT_DIR zajistěte, aby **BaltimoreCyberTrustRoot** a **DigiCertGlobalRootG2** existovaly v adresáři označeném SSL_CERT_DIR. Pokud nějaké certifikáty neexistují, vytvořte soubor s chybějícím certifikátem.

    * Pro ostatní uživatele (MySQL Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) můžete sloučit dva soubory certifikátů CA do následujícího formátu:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Nahraďte soubor PEM původní kořenové certifikační autority souborem kombinované kořenové certifikační autority a restartujte aplikaci nebo klienta.

   Po nasazení nového certifikátu na straně serveru můžete v budoucnu změnit soubor PEM certifikační autority na DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> **Baltimore certifikát** prosím vyřaďte nebo neměňte, dokud se neprovede změna certifikátu. Po provedení změny pošleme sdělení a pak bude bezpečné vyřadit **certifikát Baltimore**.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Proč se BaltimoreCyberTrustRoot certifikát nenahradil na DigiCertGlobalRootG2 během této změny od 15. února 2021?

Vyhodnotili jsme připravenost zákazníků na tuto změnu a provedli jsme to, že mnoho zákazníků si pro správu této změny vyhledalo další dobu realizace. Abychom zákazníkům poskytli více času na připravenost, rozhodli jsme se odložit změnu certifikátu na DigiCertGlobalRootG2 po dobu nejméně roku a poskytnout zákazníkům a koncovým uživatelům dostatečný čas v předstihu.

Naším doporučením pro uživatele je použít výše uvedené kroky k vytvoření kombinovaného certifikátu a připojení k serveru, ale neodstraňujte BaltimoreCyberTrustRoot certifikát, dokud pošleme oznámení, že ho odebrat.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Co když jsme odebrali certifikát BaltimoreCyberTrustRoot?

Začnete při připojování k serveru Azure Database for MySQL narazit na chyby připojení. Pro zachování připojení budete muset [nakonfigurovat SSL](howto-configure-ssl.md) pomocí certifikátu [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Pokud nepoužívám protokol SSL/TLS, musím pořád aktualizovat kořenovou certifikační autoritu?

  Pokud nepoužíváte protokol SSL/TLS, nejsou nutné žádné akce.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>Pokud používám protokol SSL/TLS, je nutné restartovat server databáze a aktualizovat tak kořenovou certifikační autoritu?

Ne, nemusíte restartovat databázový server, abyste mohli začít používat nový certifikát. Tento kořenový certifikát je změnou na straně klienta a příchozí připojení klientů musí použít nový certifikát, aby se mohl připojit k databázovému serveru.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Návody vědět, jestli používám protokol SSL/TLS s ověřováním kořenových certifikátů?

Můžete zjistit, jestli vaše připojení ověřují kořenový certifikát, a to tak, že zkontrolujete připojovací řetězec.

* Pokud váš připojovací řetězec obsahuje `sslmode=verify-ca` nebo `sslmode=verify-identity` , musíte aktualizovat certifikát.
* Pokud váš připojovací řetězec obsahuje `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` nebo `sslmode=require` , nemusíte aktualizovat certifikáty.
* Pokud v připojovacím řetězci neurčíte sslmode, nemusíte aktualizovat certifikáty.

Pokud používáte klienta, který vyabstrakce připojovací řetězec, přečtěte si dokumentaci klienta, abyste zjistili, zda ověřuje certifikáty.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>Jaký je dopad použití App Service s Azure Database for MySQL?

V případě služeb Azure App Services, které se připojují k Azure Database for MySQL, existují dva možné scénáře v závislosti na tom, jak používáte protokol SSL s vaší aplikací.

* Tento nový certifikát se přidal do App Service na úrovni platformy. Pokud používáte certifikáty SSL obsažené na platformě App Service v aplikaci, není nutné provádět žádnou akci. Toto je nejběžnější scénář.
* Pokud explicitně zahrnete cestu k souboru certifikátu SSL v kódu, pak byste si museli stáhnout nový certifikát a vytvořit Kombinovaný certifikát, jak je uvedeno výše, a použít soubor certifikátu. Dobrým příkladem tohoto scénáře je použití vlastních kontejnerů v App Service jako sdílené v [dokumentaci k App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Toto je neobvyklý scénář, ale někteří uživatelé je používají.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>Jaký je dopad použití služeb Azure Kubernetes Services (AKS) s Azure Database for MySQL?

Pokud se pokoušíte připojit k Azure Database for MySQL pomocí služeb Azure Kubernetes Services (AKS), bude se podobat přístupu z hostitelského prostředí s vyhrazenými zákazníky. Postup najdete [tady](../aks/ingress-own-tls.md).

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>Jaký je dopad použití Azure Data Factory pro připojení k Azure Database for MySQL?

Pro konektor používající Azure Integration Runtime konektor používá certifikáty v úložišti certifikátů Windows v prostředí hostovaném v Azure. Tyto certifikáty jsou již kompatibilní s nově použitými certifikáty, a proto není nutné provádět žádnou akci.

Pro konektor používající Integration Runtime v místním prostředí, kde explicitně zadáte cestu k souboru certifikátu SSL v připojovacím řetězci, budete muset stáhnout [nový certifikát](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a aktualizovat připojovací řetězec tak, aby ho používal.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>Musím při této změně naplánovat výpadek údržby databázového serveru?

No. Vzhledem k tomu, že se změna provádí pouze na straně klienta, aby se mohla připojit k databázovému serveru, není pro tuto změnu pro databázový server potřeba žádné prostoje údržby.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>Když po 15. únoru 2021 (02/15/2021) vytvořím nový server, bude to mít dopad?

Pro servery vytvořené po 15. únoru 2021 (02/15/2021) budete nadále používat [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) pro vaše aplikace pro připojení pomocí protokolu SSL.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Jak často Microsoft aktualizuje svoje certifikáty nebo jaké jsou zásady vypršení platnosti?

Certifikáty používané službou Azure Database for MySQL poskytují důvěryhodné certifikační autority (CA). Proto je podpora těchto certifikátů svázána s podporou těchto certifikátů certifikační autoritou. Platnost certifikátu [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) je naplánována na hodnotu 2025, takže společnost Microsoft bude muset před vypršením platnosti provést změnu certifikátu. V případě, že jsou v těchto předdefinovaných certifikátech nepředvídatelné chyby, společnost Microsoft bude muset provést rotaci certifikátu v nejbližším případě změny provedené 15. února 2021, aby byla služba zabezpečená a v souladu s předpisy.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Pokud používám repliky čtení, musím tuto aktualizaci provést jenom na zdrojovém serveru nebo v replikách pro čtení?

Vzhledem k tomu, že se jedná o změnu na straně klienta, pokud klient používá ke čtení dat ze serveru repliky, budete muset změny použít i pro tyto klienty.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Pokud používám replikaci dat, potřebuji provést jakoukoli akci?

Pokud pro připojení k Azure Database for MySQL používáte [replikaci dat](concepts-data-in-replication.md) , je potřeba vzít v úvahu dvě věci:

* Pokud je replikace dat z virtuálního počítače (Prem nebo virtuálního počítače Azure) na Azure Database for MySQL, musíte ověřit, jestli se k vytvoření repliky používá protokol SSL. Spusťte příkaz **Zobrazit stav podřízeného** a ověřte následující nastavení.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Pokud zjistíte, že certifikát je k dispozici pro CA_file, SSL_Cert a SSL_Key, bude nutné aktualizovat soubor přidáním [nového certifikátu](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a vytvořením kombinovaného souboru certifikátu.

* Pokud je replikace dat mezi dvěma Azure Database for MySQL servery, pak je potřeba resetovat repliku spuštěním **volání MySQL.az_replication_change_master** a zadat nový duální kořenový certifikát jako poslední parametr [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>Je k dispozici dotaz na straně serveru k určení, zda je použit protokol SSL?

Chcete-li ověřit, zda používáte připojení SSL pro připojení k serveru, postupujte podle tématu [ověřování SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Je vyžadována akce, pokud již mám v souboru certifikátu DigiCertGlobalRootG2?

No. Pokud soubor certifikátu již má **DigiCertGlobalRootG2**, není nutná žádná akce.

#### <a name="what-if-i-have-further-questions"></a>Co když mám další dotazy?

V případě otázek Získejte odpovědi od expertů komunity v [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Pokud máte plán podpory a potřebujete technickou pomoc, [kontaktujte nás](mailto:AzureDatabaseforMySQL@service.microsoft.com).
