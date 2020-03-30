---
title: Odkaz na chybu pro kontroly stavu
description: Kódy chyb a možná řešení problémů zjištěných spuštěním diagnostického příkazu az acr check-health v registru kontejnerů Azure
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289137"
---
# <a name="health-check-error-reference"></a>Odkaz na chybu kontroly stavu

Následují podrobnosti o chybových kódech vrácených příkazem [az acr check-health.][az-acr-check-health] Pro každou chybu jsou uvedena možná řešení.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Tato chyba znamená, že klient Dockeru pro rozhraní rozhraní cli nebyl nalezen. V důsledku toho nejsou spuštěny následující další kontroly: hledání verze Dockeru, vyhodnocení stavu daemon dockeru a spuštění příkazu pro vyžádat Docker.

*Potenciální řešení:* Instalace klienta Dockeru; přidejte cestu Dockeru k systémovým proměnným.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Tato chyba znamená, že stav daemon dockeru není k dispozici nebo že nebylo dosaženo pomocí rozhraní se knám. V důsledku toho docker operace `docker login` `docker pull`(například a ) nejsou k dispozici prostřednictvím cli.

*Potenciální řešení*: Restartujte daemon Dockeru nebo ověřte, zda je správně nainstalován.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazu `docker --version`PŘÍKAZ nebylo možné spustit příkaz .

*Potenciální řešení*: Zkuste spustit příkaz ručně, ujistěte se, že máte nejnovější verzi rozhraní příkazového příkazu a prozkoumejte chybovou zprávu.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Tato chyba znamená, že rozhraní se konkretním rozhraním nebylo možné vyžádat ukázkový obrázek do vašeho prostředí.

*Potenciální řešení*: Ověřte, zda všechny součásti potřebné k vytažení bitové kopie běží správně.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Tato chyba znamená, že klient Helm nemohl být nalezen rozhraním se klist, který vylučuje další operace Helm.

*Potenciální řešení*: Ověřte, zda je klient Helm nainstalován a zda je jeho cesta přidána do proměnných systémového prostředí.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Tato chyba znamená, že rozhraní rozhraní cli nebylo schopno určit nainstalovanou verzi Helm. K tomu může dojít, pokud je použita verze příkazového příkazu Azure (nebo pokud se používá verze Helm).

*Potenciální řešení*: Aktualizace na nejnovější verzi příkazového příkazového příkazu Azure nebo na doporučenou verzi Helm; Spusťte příkaz ručně a prozkoumejte chybovou zprávu.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Tato chyba znamená, že služba DNS pro daný přihlašovací server registru byla příkazem ping, ale neodpověděla, což znamená, že není k dispozici. To může znamenat některé problémy s připojením. Případně registr nemusí existovat, uživatel nemusí mít oprávnění k registru (správně načíst jeho přihlašovací server) nebo cílový registr je v jiném cloudu, než je ten, který se používá v azure cli.

*Potenciální řešení:* Ověření konektivity; ověřit pravopis registru a tento registr existuje; ověřte, že uživatel má správná oprávnění na něj a že cloud registru je stejný, který se používá v rozhraní příkazového příkazu k řešení Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Tato chyba znamená, že koncový bod výzvy pro daný registr odpověděl stavem 403 Forbidden HTTP. Tato chyba znamená, že uživatelé nemají přístup k registru, s největší pravděpodobností z důvodu konfigurace virtuální sítě. Chcete-li zobrazit aktuálně nakonfigurovaná pravidla brány firewall, spusťte program `az acr show --query networkRuleSet --name <registry>`.

*Potenciální řešení*: Odeberte pravidla virtuální sítě nebo přidejte aktuální ip adresu klienta do seznamu povolených adres.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Tato chyba znamená, že koncový bod výzvy cílového registru nevydal výzvu.

*Možná řešení*: Zkuste to znovu po určité době. Pokud chyba přetrvává, otevřete https://aka.ms/acr/issuesproblém na adrese .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Tato chyba znamená, že koncový bod výzvy cílového registru vydal výzvu, ale registr nepodporuje ověřování služby Azure Active Directory.

*Potenciální řešení*: Zkuste jiný způsob ověření, například s přihlašovacími údaji správce. Pokud uživatelé potřebují ověření pomocí služby https://aka.ms/acr/issuesAzure Active Directory, otevřete problém na adrese .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Tato chyba znamená, že přihlašovací server registru neodpověděl obnovovacím tokenem, takže byl odepřen přístup k cílovému registru. K této chybě může dojít, pokud uživatel nemá správná oprávnění v registru nebo pokud jsou pověření uživatele pro rozhraní příkazového příkazového příkazu k webu Azure zastaralá.

*Potenciální řešení*: Ověřte, zda má uživatel v registru správná oprávnění. spuštěním `az login` aktualizujte oprávnění, tokeny a pověření.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Tato chyba znamená, že přihlašovací server registru neodpověděl pomocí přístupového tokenu, takže byl odepřen přístup k cílovému registru. K této chybě může dojít, pokud uživatel nemá správná oprávnění v registru nebo pokud jsou pověření uživatele pro rozhraní příkazového příkazového příkazu k webu Azure zastaralá.

*Potenciální řešení*: Ověřte, zda má uživatel v registru správná oprávnění. spuštěním `az login` aktualizujte oprávnění, tokeny a pověření.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Tato chyba znamená, že klient nemohl navázat zabezpečené připojení k registru kontejneru. K této chybě obvykle dochází, pokud používáte nebo používáte proxy server.

*Možná řešení*: Více informací o práci za proxy lze [nalézt zde](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Tato chyba znamená, že rozhraní se konstruum nebylo možné najít přihlašovací server daného registru a nebyla nalezena žádná výchozí přípona pro aktuální cloud. K této chybě může dojít, pokud registr neexistuje, pokud uživatel nemá správná oprávnění v registru, pokud cloud registru a aktuální cloud Azure CLI neodpovídají nebo pokud je verze rozhraní příkazového příkazu Azure zastaralá.

*Potenciální řešení*: Ověřte, zda je pravopis správný a zda registr existuje. ověřte, zda má uživatel v registru správná oprávnění a zda se shodovat s mraky registru a prostředí rozhraní příkazového příkazu. aktualizujte nastavení příkazového příkazu Azure na nejnovější verzi.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Tato chyba znamená, že rozhraní se konkretním systémem není kompatibilní s aktuálně nainstalovanou verzí Docker/Notary. Zkuste přeřazení verze notáře.exe na verzi starší než 0.6.0 ručním nahrazením notáře instalace Dockeru, abyste tento problém vyřešili.

## <a name="next-steps"></a>Další kroky

Možnosti kontroly stavu registru najdete [v tématu Kontrola stavu registru kontejnerů Azure](container-registry-check-health.md).

Nejčastější dotazy a další známé problémy týkající se registru kontejnerů Azure najdete v [nejčastějších](container-registry-faq.md) dotazech.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
