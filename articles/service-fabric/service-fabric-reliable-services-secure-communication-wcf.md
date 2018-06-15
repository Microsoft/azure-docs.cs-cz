---
title: Zabezpečené komunikace služby WCF na základě v Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak zabezpečit komunikaci na základě WCF pro spolehlivé služby, které jsou spuštěny v clusteru služby Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 1304bfd376fca7eb55c4533a690a12c3fef16efc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205735"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Zabezpečená komunikace na základě WCF pro službu
Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní spolehlivé služby poskytuje několik předem komunikace zásobníky a nástroje, které můžete použít k vylepšení zabezpečení. Tento článek pojednává o tom, jak zlepšit zabezpečení, když používáte vzdálené komunikace služby.

Používáme existující [příklad](service-fabric-reliable-services-communication-wcf.md) to vysvětluje, jak nastavit zásobníku komunikace na základě WCF pro spolehlivé služby. K zajištění služby, pokud používáte zásobníku komunikace na základě WCF, postupujte takto:

1. Pro službu, potřebujete pomoc se zabezpečením naslouchací proces komunikace WCF (`WcfCommunicationListener`), které vytvoříte. Chcete-li to provést, změňte `CreateServiceReplicaListeners` metoda.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. V klientovi `WcfCommunicationClient` třídu, která byla vytvořena v předchozí [příklad](service-fabric-reliable-services-communication-wcf.md) zůstává beze změny. Je nutné přepsat, ale `CreateClientAsync` metodu `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Použití `SecureWcfCommunicationClientFactory` k vytvoření klienta WCF komunikace (`WcfCommunicationClient`). Klient použijte pro vyvolání metody služeb.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

Jako další krok, přečtěte si [webového rozhraní API s OWIN v spolehlivé služby](service-fabric-reliable-services-communication-webapi.md).
