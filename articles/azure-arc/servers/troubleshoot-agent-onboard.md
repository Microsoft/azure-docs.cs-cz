---
title: Řešení potíží s připojením agenta s povolenými servery Azure ARC
description: V tomto článku se dozvíte, jak řešit problémy s agentem připojeného počítače, který se při pokusu o připojení ke službě vyskytnout u serverů s podporou ARC Azure.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900677"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Řešení potíží s připojením agenta připojeného počítače

Tento článek poskytuje informace o řešení potíží a řešení problémů, ke kterým může dojít při pokusu o konfiguraci serverů s podporou Azure ARC připojeného agenta počítače pro Windows nebo Linux. Součástí jsou i metody interaktivního i navýšení instalace při konfiguraci připojení ke službě. Obecné informace najdete v tématu [Přehled serverů s podporou ARC](./overview.md).

## <a name="agent-verbose-log"></a>Podrobný protokol agenta

Než budete postupovat podle kroků, které jsou popsané dále v tomto článku, je potřeba mít k tomu minimální informace v podrobném protokolu. Obsahuje výstup příkazů nástroje **azcmagent** při použití argumentu verbose (-v). Soubory protokolu jsou zapisovány do `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` systému Windows a Linux do nástroje `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Windows při provádění interaktivní instalace.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Windows při provádění instalace v rámci škálování pomocí instančního objektu.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Linux při provádění interaktivní instalace.

>[!NOTE]
>Aby bylo možné spustit **azcmagent**, musíte mít oprávnění *root* Access pro počítače se systémem Linux.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Linux při provádění instalace v rámci škálování pomocí instančního objektu.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problémy s připojením agenta ke službě

V následující tabulce jsou uvedené některé známé chyby a návrhy, jak je řešit a řešit.

|Zpráva |Chyba |Pravděpodobná příčina |Řešení |
|--------|------|---------------|---------|
|Nepovedlo se získat tok zařízení autorizačního tokenu. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Koncový bod není dostupný. `login.windows.net` | Ověřte připojení ke koncovému bodu. |
|Nepovedlo se získat tok zařízení autorizačního tokenu. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy server nebo brána firewall blokuje přístup ke `login.windows.net` koncovému bodu. | Ověřte připojení ke koncovému bodu, které není blokované bránou firewall nebo proxy server. |
|Nepovedlo se získat tok zařízení autorizačního tokenu.  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Zásady skupiny objekt *Konfigurace počítače \ Šablony pro správu \ System \ profily uživatelů \ možnost Odstranit profily uživatelů starší, než je zadaný počet dnů při restartování systému* je povolená. | Ověřte, jestli je objekt zásad skupiny povolený a cílí na příslušný počítač. Další podrobnosti najdete v poznámce pod čarou <sup>[1](#footnote1)</sup> . |
|Nepovedlo se získat autorizační token ze SPN. |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy server nebo brána firewall blokuje přístup ke `login.windows.net` koncovému bodu. |Ověřte připojení ke koncovému bodu, které není blokované bránou firewall nebo proxy server. |
|Nepovedlo se získat autorizační token ze SPN. |`Invalid client secret is provided` |Chybný nebo neplatný tajný klíč instančního objektu. |Ověřte tajný klíč instančního objektu. |
| Nepovedlo se získat autorizační token ze SPN. |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Nesprávný instanční objekt nebo ID tenanta. |Ověřte instanční objekt nebo ID tenanta.|
|Získat odpověď na prostředek ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Nesprávná pověření nebo oprávnění |Ověřte, že jste vy nebo instanční objekt je členem role registrace **počítače připojeného k Azure** . |
|Nepovedlo se AzcmagentConnectovat prostředek ARM. |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Poskytovatelé prostředků Azure nejsou zaregistrovaní. |Zaregistrujte [poskytovatele prostředků](./agent-overview.md#register-azure-resource-providers). |
|Nepovedlo se AzcmagentConnectovat prostředek ARM. |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxy server nebo brána firewall blokují přístup ke `management.azure.com` koncovému bodu. |Ověřte připojení ke koncovému bodu, které není blokované bránou firewall nebo proxy server. |

<a name="footnote1"></a><sup>1</sup> Pokud je tento objekt zásad skupiny povolený a vztahuje se na počítače s připojeným agentem počítače, odstraní se profil uživatele přidružený k předdefinovanému účtu zadanému pro službu *himds* . V důsledku toho také odstraní ověřovací certifikát používaný ke komunikaci se službou uloženou v mezipaměti místního úložiště certifikátů po dobu 30 dnů. Před uplynutím 30denní lhůty se provede pokus o obnovení certifikátu. Pokud chcete tento problém vyřešit, použijte postup [zrušení registrace počítače](manage-agent.md#unregister-machine) a pak ho znovu zaregistrujte se spuštěnou službou `azcmagent connect` .

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [Microsoft Q&A](/answers/topics/azure-arc.html).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.

* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.
