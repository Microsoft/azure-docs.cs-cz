---
title: Problémy s ověřováním v Azure HDInsightu
description: Problémy s ověřováním v Azure HDInsightu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896124"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problémy s ověřováním v Azure HDInsightu

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

Na zabezpečených clusterech podporovaných Azure Data Lake (Gen1 nebo Gen2), když se uživatelé domény přihlásí ke clusterovým službám prostřednictvím brány HDI (jako je přihlášení k portálu Apache Ambari), hdi gateway se pokusí získat token OAuth z Azure Active Directory (Azure AD) jako první a potom získejte lístek Kerberos z Azure AD DS. Ověřování může selhat v některé z těchto fází. Tento článek je zaměřen na ladění některých z těchto problémů.

Pokud se ověření nezdaří, zobrazí se výzva k zadání pověření. Pokud zrušíte toto dialogové okno, bude vytištěna chybová zpráva. Zde jsou některé běžné chybové zprávy:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant nebo unauthorized_client, 50126

### <a name="issue"></a>Problém

Přihlášení se nezdaří pro federované uživatele s kódem chyby 50126 (přihlášení úspěšné pro uživatele cloudu). Chybová zpráva je podobná:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Příčina

Kód chyby Azure AD 50126 znamená, `AllowCloudPasswordValidation` že zásada nebyla nastavena klientem.

### <a name="resolution"></a>Řešení

Správce společnosti klienta Azure AD by měl povolit Azure AD používat hodnoty hashe hesel pro uživatele podporované službou ADFS.  Použijte `AllowCloudPasswordValidationPolicy` článek, který je znázorněn na článku [Použití balíčku enterprise security package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant nebo unauthorized_client, 50034

### <a name="issue"></a>Problém

Přihlášení se nezdaří s kódem chyby 50034. Chybová zpráva je podobná:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Příčina

Uživatelské jméno je nesprávné (neexistuje). Uživatel nepoužívá stejné uživatelské jméno, které se používá na webu Azure Portal.

### <a name="resolution"></a>Řešení

Použijte stejné uživatelské jméno, které funguje na tomto portálu.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant nebo unauthorized_client, 50053

### <a name="issue"></a>Problém

Uživatelský účet je uzamčen, kód chyby 50053. Chybová zpráva je podobná:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Příčina

Příliš mnoho pokusů o přihlášení s nesprávným heslem.

### <a name="resolution"></a>Řešení

Počkejte asi 30 minut, zastavte všechny aplikace, které se mohou pokoušet ověřit.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant nebo unauthorized_client, 50053

### <a name="issue"></a>Problém

Heslo vypršelo, kód chyby 50053. Chybová zpráva je podobná:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Příčina

Platnost hesla vypršela.

### <a name="resolution"></a>Řešení

Změňte heslo na webu Azure Portal (v místním systému) a počkejte 30 minut, než se synchronizace dožene.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problém

Zobrazí se `interaction_required`chybová zpráva .

### <a name="cause"></a>Příčina

Pro uživatele platí zásady podmíněného přístupu nebo vícefaktorové ověřování. Vzhledem k tomu, že interaktivní ověřování se zatím nepodporuje, je potřeba daného uživatele nebo cluster vyloučit z vícefaktorového ověřování nebo podmíněného přístupu. Pokud se rozhodnete cluster vyjmout (zásady výjimky založené na `ServiceEndpoints` IP adrese), ujistěte se, že je pro tuto virtuální síť povolena funkce AD.

### <a name="resolution"></a>Řešení

Použijte zásady podmíněného přístupu a vyjměte clustery HDInisght z vícefaktorové registrace, jak je znázorněno v [části Konfigurace clusteru HDInsight s balíčkem podnikového zabezpečení pomocí služby Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Přihlášení odepřeno

### <a name="issue"></a>Problém

Přihlášení je odepřeno.

### <a name="cause"></a>Příčina

Chcete-li se dostat do této fáze, vaše ověřování OAuth není problém, ale ověřování protokolem Kerberos je. Pokud je tento cluster zálohován službou ADLS, přihlášení OAuth bylo úspěšné před pokusem o ověřování protokolem Kerberos. V clusterech WASB se nepokouší tečovat přihlášení o oauth. Může existovat mnoho důvodů pro selhání protokolu Kerberos – jako jsou chyby hash hesel nejsou synchronizovány, uživatelský účet je uzamčen ve službě Azure AD DS a tak dále. Změny hashe hesla se synchronizují pouze v případě, že uživatel změní heslo. Při vytváření instance Azure AD DS se spustí synchronizace hesel, které se změní po vytvoření. Nebude zpětně synchronizovat hesla, která byla nastavena před jeho vznikem.

### <a name="resolution"></a>Řešení

Pokud se domníváte, že hesla nemusí být synchronizovaná, zkuste heslo změnit a počkejte několik minut na synchronizaci.

Pokuste se SSH do Budete muset pokusit ověřit (kinit) pomocí stejné ho uživatele pověření, z počítače, který je připojen k doméně. SSH do hlavy / okraj uzlu s místním uživatelem a pak spusťte kinit.

---

## <a name="kinit-fails"></a>kinit selže

### <a name="issue"></a>Problém

Kinit selže.

### <a name="cause"></a>Příčina

Liší.

### <a name="resolution"></a>Řešení

Chcete-li kinit uspět, musíte `sAMAccountName` znát svůj (to je krátký název účtu bez říše). `sAMAccountName`je obvykle předpona účtu `bob@contoso.com`(jako bob in). Pro některé uživatele by to mohlo být jiné. Budete potřebovat možnost procházet / hledat v `sAMAccountName`adresáři se učit své .

Způsoby, `sAMAccountName`jak najít :

* Pokud se můžete přihlásit do Ambari pomocí místního správce Ambari, podívejte se na seznam uživatelů.

* Pokud máte [počítač se systémem Windows spojený s doménou](../../active-directory-domain-services/manage-domain.md), můžete k procházení použít standardní nástroje služby Windows AD. To vyžaduje funkční účet v doméně.

* Z hlavního uzlu můžete k vyhledávání použít příkazy SAMBA. To vyžaduje platnou relaci protokolu Kerberos (úspěšné kinit). net ads search "(userPrincipalName=bob*)"

    Výsledky hledání / procházení by `sAMAccountName` vám měly ukázat atribut. Také se můžete podívat na `pwdLastSet` `badPasswordTime`jiné `userPrincipalName` atributy, jako je , , atd., aby zjistili, zda tyto vlastnosti odpovídají tomu, co očekáváte.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit se nezdaří s chybou předběžného ověřování

### <a name="issue"></a>Problém

Kinit selže `Preauthentication` s neúspěchem.

### <a name="cause"></a>Příčina

Nesprávné uživatelské jméno nebo heslo.

### <a name="resolution"></a>Řešení

Zkontrolujte své uživatelské jméno a heslo. Zkontrolujte také další vlastnosti popsané výše. Chcete-li povolit podrobné ladění, `export KRB5_TRACE=/tmp/krb.log` spusťte z relace před pokusem kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Příkaz Úloha / HDFS se nezdaří z důvodu tokennotfoundexception

### <a name="issue"></a>Problém

Příkaz Úloha / HDFS se `TokenNotFoundException`nezdaří z důvodu .

### <a name="cause"></a>Příčina

Požadovaný přístupový token OAuth nebyl nalezen, aby úloha / příkaz proběhlúspěšně. Ovladač ADLS / ABFS se pokusí načíst přístupový token OAuth ze služby pověření před provedením požadavků na úložiště. Tento token získá zaregistrovat při přihlášení k portálu Ambari pomocí stejného uživatele.

### <a name="resolution"></a>Řešení

Ujistěte se, že jste se úspěšně přihlásili k portálu Ambari jednou prostřednictvím uživatelského jména, jehož identita se používá ke spuštění úlohy.

---

## <a name="error-fetching-access-token"></a>Při načítání přístupového tokenu došlo k chybě.

### <a name="issue"></a>Problém

Uživatel obdrží chybovou zprávu `Error fetching access token`.

### <a name="cause"></a>Příčina

K této chybě dochází nepravidelně, když se uživatelé pokusí o přístup k ADLS Gen2 pomocí aklů a platnost tokenu Kerberos vypršela.

### <a name="resolution"></a>Řešení

* Pro Azure Data Lake Storage Gen1 vyčistěte mezipaměť prohlížeče a znovu se přihlaste k Ambari.

* Pro Azure Data Lake Storage `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Gen2 spustit pro uživatele, který se pokouší přihlásit jako

---

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
