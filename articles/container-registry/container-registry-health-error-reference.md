---
title: Chyba odkazu pro kontrolu stavu – Azure Container Registry
description: Kódy chyb a možná řešení problémy nalezené pomocí příkazu az acr stav kontroly diagnostiky ve službě Azure Container Registry
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555108"
---
# <a name="health-check-error-reference"></a>Reference k chybám kontroly stavu

Toto jsou podrobnosti o kódů chyb vrácených nástrojem [az acr-stav kontroly][az-acr-check-health] příkazu. U každé chyby jsou uvedené možné řešení.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Tato chyba znamená, že klienta Docker pro rozhraní příkazového řádku se nenašla. V důsledku toho nejsou, spusťte následující další kontroly: Vyhledání stavu démona Dockeru verze, vyhodnocování Dockeru a spuštění Docker pull příkazu.

*Možná řešení*: Nainstalujte klienta Dockeru. přidáte cestu Dockeru k systémovým proměnným.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Tato chyba znamená, že stav démona Dockeru není k dispozici nebo že to není dosažitelná pomocí rozhraní příkazového řádku. Ve výsledku operace Dockeru (například `docker login` a `docker pull`) nejsou k dispozici prostřednictvím rozhraní příkazového řádku.

*Možná řešení*: Restartujte démona Dockeru nebo ověřit, zda je správně nainstalován.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku se nepodařilo spustit příkaz `docker --version`.

*Možná řešení*: Zkuste spustit příkaz ručně, ujistěte se, že máte nejnovější verzi rozhraní příkazového řádku a prozkoumejte chybová zpráva.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Tato chyba znamená, že rozhraní příkazového řádku nebyl schopen stáhnout ukázkový obrázek do vašeho prostředí.

*Možná řešení*: Ověřte, že jsou všechny komponenty potřebné k vyžádání obrázku správně funguje.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Tato chyba znamená, že tento příkaz Helm klient nebyl nalezen pomocí rozhraní příkazového řádku, který vylučuje jiné operace Helm.

*Možná řešení*: Ověřte, že je nainstalován klient Helm, a že jeho cesty je přidaný do proměnných prostředí systému.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku nebyl schopen určit nainstalovanou verzi Helm. K tomu může dojít, pokud verze rozhraní příkazového řádku Azure (nebo, pokud verze Helm) používá je zastaralý.

*Možná řešení*: Aktualizovat na nejnovější verzi rozhraní příkazového řádku Azure nebo na doporučenou verzi Helm; Spusťte příkaz ručně a prozkoumat chybová zpráva.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Tato chyba znamená, že DNS pro daného registru přihlašovací server byl příkaz ping, ale neodpověděl, což znamená, že není k dispozici. To může znamenat některé problémy s připojením. Můžete také nemusí existovat v registru, uživatel možná nemáte oprávnění k registru (správně načíst jeho přihlašovací server) nebo registru cílového probíhá jiný cloud, než jaký se používá v Azure CLI.

*Možná řešení*: Ověření připojení; Zkontrolujte pravopis registru a tohoto registru existuje. Ověřte, že uživatel má správná oprávnění na něj a, cloudu v registru je stejný, který se používá v Azure CLI.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Tato chyba znamená, že challenge koncový bod pro daného registru odpověděl stavem 403 Zakázáno HTTP. Tato chyba znamená, že uživatelé nemají přístup k registru, pravděpodobně z důvodu konfigurace virtuální sítě.

*Možná řešení*: Odeberte pravidla virtuální sítě, nebo přidejte aktuální IP adresu klienta do seznamu povolených.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Tato chyba znamená, že koncový bod challenge cílového registru nevydala příkaz náročné.

*Možná řešení*: Zkuste to znovu za chvíli. Pokud potíže potrvají, otevřete problém na https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Tato chyba znamená, že koncový bod challenge cílového registru vydány složité, ale registru nepodporuje ověřování Azure Active Directory.

*Možná řešení*: Zkuste jiný způsob, jak ověřit například pomocí přihlašovacích údajů správce. Pokud uživatelé potřebují k ověření pomocí Azure Active Directory, otevřete problém na https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Tato chyba znamená, aby byl odepřen přístup k registru cílového neodpověděl přihlašovacího serveru registru se obnovovací token. Této chybě může dojít, pokud uživatel nemá správná oprávnění registru nebo pokud jsou zastaralé přihlašovací údaje uživatele pro Azure CLI.

*Možná řešení*: Ověřte, zda má uživatel oprávnění na registru. Spustit `az login` aktualizovat oprávnění, tokenů a přihlašovací údaje.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Tato chyba znamená, že přihlašovací server registru neodpověděl s přístupovým tokenem, tak, aby byl odepřen přístup k registru cíl. Této chybě může dojít, pokud uživatel nemá správná oprávnění registru nebo pokud jsou zastaralé přihlašovací údaje uživatele pro Azure CLI.

*Možná řešení*: Ověřte, zda má uživatel oprávnění na registru. Spustit `az login` aktualizovat oprávnění, tokenů a přihlašovací údaje.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Tato chyba znamená, že rozhraní příkazového řádku se nepodařilo najít přihlašovací server daného registru a pro aktuálního cloudu nebyl nalezen žádný výchozí příponou. Této chybě může dojít, pokud v registru neexistuje, pokud uživatel nemá správná oprávnění k registru, pokud v registru a aktuální cloudem Azure CLI se neshodují, nebo pokud je zastaralá verze rozhraní příkazového řádku Azure.

*Možná řešení*: Ověřte, zda je správný a zda existuje registr; Ověřte má správná oprávnění k registru, a odpovídající cloudy registru a prostředí rozhraní příkazového řádku; Aktualizujte na nejnovější verzi Azure CLI.

## <a name="next-steps"></a>Další postup

Možnosti pro kontrolu stavu registru najdete v tématu [Kontrola stavu služby Azure container registry](container-registry-check-health.md).

Zobrazit [nejčastější dotazy k](container-registry-faq.md) pro nejčastější dotazy a dalších známých problémech o Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
