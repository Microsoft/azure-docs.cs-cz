---
title: Problémy s ověřováním ve službě Azure HDInsight
description: Problémy s ověřováním ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: b5a55b3b5f9affcd9f34e1c0d4c439a7ada8c0b9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095120"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problémy s ověřováním ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

U zabezpečených clusterů, které jsou zajištěny Azure Data Lake (Gen1 nebo Gen2), když se uživatelé domény přihlašují ke službám clusteru prostřednictvím brány HDI (například přihlášení k portálu Apache Ambari), služba HDI Gateway se pokusí získat token OAuth z Azure Active Directory (Azure AD) jako první a pak získat lístek protokolu Kerberos z Azure služba AD DS. Ověřování může selhat v některé z těchto fází. Tento článek se zaměřuje na ladění některých těchto problémů.

Pokud se ověření nezdaří, zobrazí se výzva k zadání přihlašovacích údajů. Pokud zrušíte toto dialogové okno, bude vytištěna chybová zpráva. Tady jsou některé běžné chybové zprávy:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant nebo unauthorized_client, 50126

### <a name="issue"></a>Problém

Přihlášení pro federované uživatele se nezdařilo s kódem chyby 50126 (přihlášení pro uživatele v cloudu je úspěšné). Chybová zpráva vypadá nějak takto:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Příčina

Kód chyby Azure AD 50126 znamená, že `AllowCloudPasswordValidation` klient nestavil zásady.

### <a name="resolution"></a>Řešení

Globální správce tenanta Azure AD by měl povolit, aby služba Azure AD používala hodnoty hash hesel pro uživatele se službou AD FS.  Použijte, `AllowCloudPasswordValidationPolicy` jak je uvedeno v článku [použití balíček zabezpečení podniku ve službě HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant nebo unauthorized_client, 50034

### <a name="issue"></a>Problém

Přihlášení se nezdařilo s kódem chyby 50034. Chybová zpráva vypadá nějak takto:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Příčina

Uživatelské jméno je nesprávné (neexistuje). Uživatel nepoužívá stejné uživatelské jméno, které se používá v Azure Portal.

### <a name="resolution"></a>Řešení

Použijte stejné uživatelské jméno, které funguje na portálu.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant nebo unauthorized_client, 50053

### <a name="issue"></a>Problém

Uživatelský účet je uzamčený, kód chyby 50053. Chybová zpráva vypadá nějak takto:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Příčina

Příliš mnoho pokusů o přihlášení s nesprávným heslem

### <a name="resolution"></a>Řešení

Počkejte 30 minut nebo zastavte všechny aplikace, které se mohou pokoušet ověřit.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant nebo unauthorized_client, 50053 (#2)

### <a name="issue"></a>Problém

Platnost hesla vypršela, kód chyby 50053. Chybová zpráva vypadá nějak takto:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Příčina

Platnost hesla vypršela.

### <a name="resolution"></a>Řešení

Změňte heslo v Azure Portal (v místním systému) a potom počkejte 30 minut, než se synchronizace zachytí.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problém

Zobrazí se chybová zpráva `interaction_required` .

### <a name="cause"></a>Příčina

Pro uživatele platí zásady podmíněného přístupu nebo vícefaktorové ověřování. Vzhledem k tomu, že interaktivní ověřování se zatím nepodporuje, je potřeba daného uživatele nebo cluster vyloučit z vícefaktorového ověřování nebo podmíněného přístupu. Pokud se rozhodnete vyloučit cluster (zásady výjimky na základě IP adresy), ujistěte se, že je služba AD `ServiceEndpoints` pro tuto virtuální síť povolená.

### <a name="resolution"></a>Řešení

Použijte zásady podmíněného přístupu a vylučte clustery HDInsight od MFA, jak je znázorněno v části [Konfigurace clusteru HDInsight s balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Přihlášení odepřeno

### <a name="issue"></a>Problém

Přihlášení bylo odepřeno.

### <a name="cause"></a>Příčina

V této fázi není ověřování OAuth problémem, ale ověřování protokolem Kerberos je. Pokud je tento cluster zálohovaný pomocí ADLS, přihlášení OAuth se úspěšně přihlásilo předtím, než se pokusí ověřování protokolem Kerberos. U clusterů WASB se přihlášení OAuth nepokouší. Mohlo by se jednat o spoustu důvodů selhání protokolu Kerberos, protože hodnoty hash hesla se nesynchronizují, uživatelský účet byl uzamčen v Azure služba AD DS atd. Hodnoty hash hesla se synchronizují pouze v případě, že uživatel změní heslo. Při vytváření instance služby Azure služba AD DS spustí synchronizaci hesel, která se po vytvoření změnila. Nebude se zpětně synchronizovat hesla, která byla nastavena před jeho zahájením.

### <a name="resolution"></a>Řešení

Pokud si myslíte, že hesla možná nebudou synchronizovaná, zkuste změnit heslo a počkejte několik minut, než se synchronizuje.

Pokusíte-li se SSH do služby, budete se muset pokusit ověřit (kinit) pomocí stejných přihlašovacích údajů uživatele z počítače, který je připojený k doméně. Připojte se přes SSH k uzlu Head/Edge s místním uživatelem a potom spusťte kinit.

---

## <a name="kinit-fails"></a>kinit se nezdařila

### <a name="issue"></a>Problém

Kinit se nezdařila.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Řešení

Aby kinit bylo úspěšné, musíte znát `sAMAccountName` (Jedná se o krátký název účtu bez sféry). `sAMAccountName` je obvykle předpona účtu (například Bob in `bob@contoso.com` ). Pro některé uživatele se může lišit. Abyste se dozvěděli, budete potřebovat možnost Procházet adresář a vyhledat si ho `sAMAccountName` .

Způsoby, jak najít `sAMAccountName` :

* Pokud se můžete přihlásit k Ambari pomocí místního správce Ambari, podívejte se na seznam uživatelů.

* Pokud máte počítač s [Windows připojen k doméně](../../active-directory-domain-services/tutorial-create-management-vm.md), můžete k procházení použít standardní nástroje Windows AD. To vyžaduje pracovní účet v doméně.

* Z hlavního uzlu můžete k hledání použít příkazy služby SAMBA. To vyžaduje platnou relaci protokolu Kerberos (úspěšné kinit). NET ADS Search "(userPrincipalName = Bob *)"

    Výsledky hledání nebo procházení by měly ukazovat na `sAMAccountName` atribut. Také se můžete podívat na další atributy, například `pwdLastSet` , `badPasswordTime` `userPrincipalName` atd., pokud se tyto vlastnosti shodují s očekávaným způsobem.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit selže s chybou předběžného ověření

### <a name="issue"></a>Problém

Kinit selže s `Preauthentication` chybou.

### <a name="cause"></a>Příčina

Nesprávné uživatelské jméno nebo heslo.

### <a name="resolution"></a>Řešení

Ověřte uživatelské jméno a heslo. Také se podívejte na další vlastnosti popsané výše. Pokud chcete povolit podrobné ladění, spusťte `export KRB5_TRACE=/tmp/krb.log` z relace před tím, než zkusíte kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Příkaz Job/HDFS se nezdařil z důvodu TokenNotFoundException

### <a name="issue"></a>Problém

Příkaz Job/HDFS se z důvodu nezdařil `TokenNotFoundException` .

### <a name="cause"></a>Příčina

Požadovaný přístupový token OAuth se nenašel pro úspěšné provedení úlohy nebo příkazu. Ovladač ADLS/ABFS se pokusí načíst přístupový token OAuth ze služby přihlašovacích údajů před vytvořením požadavků na úložiště. Tento token se zaregistruje při přihlášení k portálu Ambari pomocí stejného uživatele.

### <a name="resolution"></a>Řešení

Ujistěte se, že jste se úspěšně přihlásili k portálu Ambari, a to pomocí uživatelského jména, jehož identita se používá ke spuštění úlohy.

---

## <a name="error-fetching-access-token"></a>Chyba při načítání přístupového tokenu

### <a name="issue"></a>Problém

Uživatel obdrží chybovou zprávu `Error fetching access token` .

### <a name="cause"></a>Příčina

K této chybě dochází občas, když se uživatelé pokusí o přístup k ADLS Gen2 pomocí seznamů ACL a platnost tokenu protokolu Kerberos vypršela.

### <a name="resolution"></a>Řešení

* V případě Azure Data Lake Storage Gen1 vyčistěte mezipaměť prohlížeče a znovu se přihlaste do Ambari.

* U Azure Data Lake Storage Gen2 spustit `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` pro uživatele, který se pokouší přihlásit jako

---

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]