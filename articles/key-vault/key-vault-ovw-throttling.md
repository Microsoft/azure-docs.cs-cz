---
title: Doprovodné materiály k omezování služby Azure Key Vault
description: Omezení služby Key Vault omezuje počet souběžných volání, aby se zabránilo nadměrnému využití prostředků.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: e5ffd6bcda20b04d3ba42a98b3c5fd75327c4710
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099237"
---
# <a name="azure-key-vault-throttling-guidance"></a>Doprovodné materiály k omezování služby Azure Key Vault

Omezení šířky pásma je proces, který spustíte funkci, která omezuje počet souběžných volání do služby Azure, aby se zabránilo nadměrnému využití prostředků. Azure Key Vaultu (AKV) je navržená k velkému počtu požadavků. Pokud dojde k příliš velkého počtu požadavků, omezování požadavků vašeho klienta pomáhá udržet optimální výkon a spolehlivost služby službou AZURE.

Omezení se liší v závislosti na scénáři. Například pokud provádíte velké objemy zápisy, možnost pro omezení je vyšší než pokud pouze provádíte čtení.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak Key Vault zpracovává jeho omezení?

Existují omezení služby ve službě Key Vault k zabránění zneužití prostředky a zajistit kvalitu služby pro všechny klienty služby Key Vault. Při překročení prahové hodnoty služby Key Vault omezení všechny další žádosti od tohoto klienta pro určitou dobu. Pokud k tomu dojde, Key Vault vrátí stavový kód HTTP 429 (příliš mnoho požadavků), a neúspěšné požadavky. Navíc neúspěšné požadavky, které vracejí 429 počet směrem k limitech omezení sledován pomocí funkce služby Key Vault. 

Pokud budete mít obchodní případ pro vyšší omezení limity, kontaktujte nás prosím.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Omezení vaší aplikace v reakci na omezení služby

Následují **osvědčené postupy** pro omezení vaší aplikace:
- Snížit počet operací na požadavek.
- Snižte frekvenci požadavků.
- Vyhněte se okamžité opakování. 
    - Všechny požadavky kumulovat proti vaší limity využití.

Při implementaci vaší aplikace pro zpracování chyb, použijte k detekci potřebu omezování na straně klienta kód chyby protokolu HTTP 429. Pokud požadavek selže s kódem chyby HTTP 429 znovu, že máte stále omezení služby Azure. Dál používat doporučené straně klienta omezování metoda, opakování žádosti, dokud nebude úspěšný.

Kód, který implementuje exponenciální regresí je uveden níže. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Pomocí tohoto kódu v klientovi C\# aplikace (jiného webového rozhraní API klienta mikroslužeb, aplikaci ASP.NET MVC nebo dokonce C\# aplikace Xamarin) je jednoduché. Následující příklad ukazuje, jak pomocí třídy HttpClient.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Mějte na paměti, že tento kód je vhodný pouze jako testování konceptu. 

### <a name="recommended-client-side-throttling-method"></a>Doporučenou metodou omezování na straně klienta

Na kód chyby protokolu HTTP 429 začněte omezování klienta pomocí exponenciálního omezení rychlosti přístupu:

1. Počkejte 1 sekundu, opakování žádosti
2. Pokud i nadále omezený počkejte 2 sekundy, zkuste žádost
3. Pokud i nadále omezený čekání 4 sekundy opakujte požadavek
4. Pokud i nadále omezený čekání 8 sekund opakujte požadavek
5. Pokud i nadále omezený čekání 16 sekund opakujte požadavek

V tomto okamžiku jste by neměly zobrazovat kódy odpovědí protokolu HTTP 429.

## <a name="see-also"></a>Další informace najdete v tématech

Lepší orientaci omezení využití sítě v cloudu Microsoftu, najdete v části [model omezení využití sítě](https://docs.microsoft.com/azure/architecture/patterns/throttling).

