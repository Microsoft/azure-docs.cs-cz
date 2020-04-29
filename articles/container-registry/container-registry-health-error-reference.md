---
title: Referenční informace o chybě pro kontroly stavu
description: Kódy chyb a možná řešení problémů nalezené spuštěním příkazu AZ ACR check-Health Diagnostic v Azure Container Registry
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289137"
---
# <a name="health-check-error-reference"></a>Informace o chybě kontroly stavu

Níže jsou uvedeny podrobnosti o kódech chyb vrácených příkazem [AZ ACR check-Health][az-acr-check-health] . Pro každou chybu jsou uvedena možná řešení.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Tato chyba znamená, že se nepovedlo najít klienta Docker pro rozhraní příkazového řádku. V důsledku toho následující další kontroly nebudou spuštěny: hledání verze Docker, vyhodnocení stavu démona Docker a spuštění příkazu Docker Pull.

*Potenciální řešení*: nainstalovat klienta Docker; Přidejte do systémových proměnných cestu Docker.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Tato chyba znamená, že stav démona Docker není k dispozici nebo že k němu nelze získat přístup pomocí rozhraní příkazového řádku. V důsledku toho nejsou k dispozici operace Docker `docker login` ( `docker pull`například a) prostřednictvím rozhraní příkazového řádku.

*Potenciální řešení*: Restartujte démona Docker nebo ověřte, že je správně nainstalovaný.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku (CLI) `docker --version`nemohlo spustit příkaz.

*Potenciální řešení*: Zkuste spustit příkaz ručně, ujistěte se, že máte nejnovější verzi rozhraní příkazového řádku, a prozkoumejte chybovou zprávu.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Tato chyba znamená, že rozhraní příkazového řádku (CLI) nedokázalo načíst ukázkovou image do vašeho prostředí.

*Potenciální řešení*: Ověřte, že všechny součásti potřebné k načtení image jsou spuštěné správně.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Tato chyba znamená, že rozhraní příkazového řádku Helm nemůže najít klienta, což vylučuje jiné operace Helm.

*Potenciální řešení*: Ověřte, že je nainstalovaný klient Helm a že se jeho cesta přidá do proměnných prostředí systému.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku se nepodařilo zjistit nainstalovanou verzi Helm. K tomu může dojít v případě, že se používá verze Azure CLI (nebo pokud je verze Helm) zastaralá.

*Potenciální řešení*: aktualizujte na nejnovější verzi rozhraní příkazového řádku Azure CLI nebo na doporučenou verzi Helm. Spusťte příkaz ručně a prozkoumejte chybovou zprávu.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Tato chyba znamená, že DNS pro daný přihlašovací server registru byl testovat pomocí testu, ale neodpověděl, což znamená, že není k dispozici. To může znamenat problémy s připojením. Případně může registr existovat, ale uživatel nemusí mít oprávnění k registru (aby mohl správně načíst svůj přihlašovací server), nebo cílový registr je v jiném cloudu než ten, který se používá v rozhraní příkazového řádku Azure CLI.

*Potenciální řešení*: ověřit připojení; Ověřte pravopis registru a tento registr existuje. Ověřte, zda má uživatel správná oprávnění a zda je Cloud registru stejný, který se používá v rozhraní příkazového řádku Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Tato chyba znamená, že koncový bod výzvy pro daný registr odpověděl s 403 zakázaným stavem protokolu HTTP. Tato chyba znamená, že uživatelé nemají přístup k registru, pravděpodobně kvůli konfiguraci virtuální sítě. Chcete-li zobrazit aktuálně konfigurovaná pravidla brány firewall `az acr show --query networkRuleSet --name <registry>`, spusťte příkaz.

*Potenciální řešení*: odebrat pravidla virtuální sítě nebo přidat aktuální IP adresu klienta do seznamu povolených.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Tato chyba znamená, že koncový bod výzvy cílového registru nevydal výzvu.

*Potenciální řešení*: zkuste to za chvíli znovu. Pokud chyba přetrvává, otevřete problém na adrese https://aka.ms/acr/issues.

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Tato chyba znamená, že koncový bod výzvy cílového registru vystavil výzvu, ale registr nepodporuje Azure Active Directory ověřování.

*Potenciální řešení*: Vyzkoušejte jiný způsob ověřování, například s přihlašovacími údaji správce. Pokud se uživatelé potřebují ověřit pomocí Azure Active Directory, otevřete problém na adrese https://aka.ms/acr/issues.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Tato chyba znamená, že přihlašovací server registru neodpověděl s tokenem pro obnovení, takže přístup k cílovému registru byl odepřen. K této chybě může dojít, pokud uživatel nemá správná oprávnění k registru nebo pokud jsou přihlašovací údaje uživatele pro rozhraní příkazového řádku Azure zastaralé.

*Potenciální řešení*: Ověřte, jestli má uživatel správná oprávnění k registru. Spusťte `az login` příkaz k aktualizaci oprávnění, tokenů a přihlašovacích údajů.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Tato chyba znamená, že přihlašovací server registru neodpověděl pomocí přístupového tokenu, aby byl odepřen přístup k cílovému registru. K této chybě může dojít, pokud uživatel nemá správná oprávnění k registru nebo pokud jsou přihlašovací údaje uživatele pro rozhraní příkazového řádku Azure zastaralé.

*Potenciální řešení*: Ověřte, jestli má uživatel správná oprávnění k registru. Spusťte `az login` příkaz k aktualizaci oprávnění, tokenů a přihlašovacích údajů.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Tato chyba znamená, že klient nemohl navázat zabezpečené připojení k registru kontejneru. K této chybě obvykle dochází v případě, že používáte nebo používáte proxy server.

*Možná řešení*: Další informace o práci za proxy serverem najdete [tady](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Tato chyba znamená, že rozhraní příkazového řádku se nepodařilo najít přihlašovací server daného registru a nebyla nalezena žádná výchozí přípona pro aktuální Cloud. K této chybě může dojít, pokud registr neexistuje, pokud uživatel nemá správná oprávnění k registru, pokud se Cloud registru a aktuální cloud Azure CLI neshodují, nebo pokud je verze Azure CLI zastaralá.

*Potenciální řešení*: Ověřte, zda je pravopis správné a zda registr existuje. Ověřte, zda má uživatel správná oprávnění k registru a zda se shodují cloudy registru a prostředí CLI. Aktualizujte rozhraní příkazového řádku Azure na nejnovější verzi.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku není kompatibilní s aktuálně nainstalovanou verzí Docker/notáře. Vyzkoušejte downgrade vaší verze souboru notář. exe na verzi starší než 0.6.0, a to tak, že tento problém vyřešíte tak, že ručně nahradíte svého správce notáře instalace Docker.

## <a name="next-steps"></a>Další kroky

Možnosti, jak kontrolovat stav registru, najdete v tématu o [kontrole stavu služby Azure Container Registry](container-registry-check-health.md).

Nejčastější dotazy a další známé problémy týkající se Azure Container Registry najdete v [nejčastějších](container-registry-faq.md) dotazech.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
