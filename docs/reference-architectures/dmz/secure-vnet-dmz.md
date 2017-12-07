---
title: "Implementación de una red perimetral entre Azure e Internet"
description: "Cómo implementar una arquitectura de red híbrida segura con acceso a Internet en Azure."
author: telmosampaio
ms.date: 11/23/2016
pnp.series.title: Network DMZ
pnp.series.next: nva-ha
pnp.series.prev: secure-vnet-hybrid
cardTitle: DMZ between Azure and the Internet
ms.openlocfilehash: 372d5bb0fc0e3c272843e062210dec5c15b2b78a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="dmz-between-azure-and-the-internet"></a><span data-ttu-id="0e365-103">Red perimetral entre Internet y Azure</span><span class="sxs-lookup"><span data-stu-id="0e365-103">DMZ between Azure and the Internet</span></span>

<span data-ttu-id="0e365-104">Esta arquitectura de referencia muestra una red híbrida segura que extiende una red local a Azure y también acepta el tráfico de Internet.</span><span class="sxs-lookup"><span data-stu-id="0e365-104">This reference architecture shows a secure hybrid network that extends an on-premises network to Azure and also accepts Internet traffic.</span></span> 

<span data-ttu-id="0e365-105">[![0]][0]</span><span class="sxs-lookup"><span data-stu-id="0e365-105">[![0]][0]</span></span> 

<span data-ttu-id="0e365-106">*Descargue un [archivo Visio][visio-download] de esta arquitectura.*</span><span class="sxs-lookup"><span data-stu-id="0e365-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="0e365-107">Esta arquitectura de referencia extiende la arquitectura descrita en [Implementación de una red perimetral entre Azure y el centro de datos local][implementing-a-secure-hybrid-network-architecture].</span><span class="sxs-lookup"><span data-stu-id="0e365-107">This reference architecture extends the architecture described in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture].</span></span> <span data-ttu-id="0e365-108">Agrega una red perimetral pública que gestiona el tráfico de Internet, además de la red perimetral privada que gestiona el tráfico de la red local.</span><span class="sxs-lookup"><span data-stu-id="0e365-108">It adds a public DMZ that handles Internet traffic, in addition to the private DMZ that handles traffic from the on-premises network</span></span> 

<span data-ttu-id="0e365-109">Los usos habituales de esta arquitectura incluyen:</span><span class="sxs-lookup"><span data-stu-id="0e365-109">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="0e365-110">Aplicaciones híbridas donde parte de las cargas de trabajo se ejecutan de forma local y parte en Azure.</span><span class="sxs-lookup"><span data-stu-id="0e365-110">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
* <span data-ttu-id="0e365-111">Infraestructura de Azure que enruta el tráfico entrante procedente del entorno local e Internet.</span><span class="sxs-lookup"><span data-stu-id="0e365-111">Azure infrastructure that routes incoming traffic from on-premises and the Internet.</span></span>

## <a name="architecture"></a><span data-ttu-id="0e365-112">Arquitectura</span><span class="sxs-lookup"><span data-stu-id="0e365-112">Architecture</span></span>

<span data-ttu-id="0e365-113">La arquitectura consta de los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="0e365-113">The architecture consists of the following components.</span></span>

* <span data-ttu-id="0e365-114">**Dirección IP pública (PIP)**</span><span class="sxs-lookup"><span data-stu-id="0e365-114">**Public IP address (PIP)**.</span></span> <span data-ttu-id="0e365-115">La dirección IP del punto de conexión público.</span><span class="sxs-lookup"><span data-stu-id="0e365-115">The IP address of the public endpoint.</span></span> <span data-ttu-id="0e365-116">Los usuarios externos conectados a Internet pueden acceder al sistema mediante esta dirección.</span><span class="sxs-lookup"><span data-stu-id="0e365-116">External users connected to the Internet can access the system through this address.</span></span>
* <span data-ttu-id="0e365-117">**Aplicación virtual de red (NVA)**.</span><span class="sxs-lookup"><span data-stu-id="0e365-117">**Network virtual appliance (NVA)**.</span></span> <span data-ttu-id="0e365-118">Esta arquitectura incluye un grupo independiente de NVA para el tráfico que se origina en Internet.</span><span class="sxs-lookup"><span data-stu-id="0e365-118">This architecture includes a separate pool of NVAs for traffic originating on the Internet.</span></span>
* <span data-ttu-id="0e365-119">**Azure Load Balancer**.</span><span class="sxs-lookup"><span data-stu-id="0e365-119">**Azure load balancer**.</span></span> <span data-ttu-id="0e365-120">Todas las solicitudes entrantes de Internet pasan por el equilibrador de carga y se distribuyen a las aplicaciones virtuales de red en la red perimetral pública.</span><span class="sxs-lookup"><span data-stu-id="0e365-120">All incoming requests from the Internet pass through the load balancer and are distributed to the NVAs in the public DMZ.</span></span>
* <span data-ttu-id="0e365-121">**Subred de entrada de red perimetral pública**.</span><span class="sxs-lookup"><span data-stu-id="0e365-121">**Public DMZ inbound subnet**.</span></span> <span data-ttu-id="0e365-122">Esta subred acepta las solicitudes del equilibrador de carga de Azure.</span><span class="sxs-lookup"><span data-stu-id="0e365-122">This subnet accepts requests from the Azure load balancer.</span></span> <span data-ttu-id="0e365-123">Las solicitudes entrantes se pasan a una de las aplicaciones virtuales de red de la red perimetral pública.</span><span class="sxs-lookup"><span data-stu-id="0e365-123">Incoming requests are passed to one of the NVAs in the public DMZ.</span></span>
* <span data-ttu-id="0e365-124">**Subred de salida de red perimetral pública**.</span><span class="sxs-lookup"><span data-stu-id="0e365-124">**Public DMZ outbound subnet**.</span></span> <span data-ttu-id="0e365-125">Las solicitudes que apueba la aplicación virtual de red pasan por esta subred y se dirigen al equilibrador de carga interno en el nivel web.</span><span class="sxs-lookup"><span data-stu-id="0e365-125">Requests that are approved by the NVA pass through this subnet to the internal load balancer for the web tier.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0e365-126">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="0e365-126">Recommendations</span></span>

<span data-ttu-id="0e365-127">Las siguientes recomendaciones sirven para la mayoría de los escenarios.</span><span class="sxs-lookup"><span data-stu-id="0e365-127">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="0e365-128">Sígalas a menos que tenga un requisito concreto que las invalide.</span><span class="sxs-lookup"><span data-stu-id="0e365-128">Follow these recommendations unless you have a specific requirement that overrides them.</span></span> 

### <a name="nva-recommendations"></a><span data-ttu-id="0e365-129">Recomendaciones para NVA</span><span class="sxs-lookup"><span data-stu-id="0e365-129">NVA recommendations</span></span>

<span data-ttu-id="0e365-130">Se recomienda usar un conjunto de NVA para el tráfico que se origina en Internet y otro para el tráfico que se origina en el entorno local.</span><span class="sxs-lookup"><span data-stu-id="0e365-130">Use one set of NVAs for traffic originating on the Internet, and another for traffic originating on-premises.</span></span> <span data-ttu-id="0e365-131">Utilizar únicamente un conjunto de NVA para ambos es un riesgo de seguridad, ya que no proporciona ningún perímetro de seguridad entre los dos conjuntos de tráfico de red.</span><span class="sxs-lookup"><span data-stu-id="0e365-131">Using only one set of NVAs for both is a security risk, because it provides no security perimeter between the two sets of network traffic.</span></span> <span data-ttu-id="0e365-132">El uso de NVA independientes reduce la complejidad de la comprobación de las reglas de seguridad y deja claro qué reglas se corresponden con cada solicitud de red entrante.</span><span class="sxs-lookup"><span data-stu-id="0e365-132">Using separate NVAs reduces the complexity of checking security rules, and makes it clear which rules correspond to each incoming network request.</span></span> <span data-ttu-id="0e365-133">Un conjunto de NVA implementa reglas solo para el tráfico de Internet, mientras que otro lo hace solo para el tráfico local.</span><span class="sxs-lookup"><span data-stu-id="0e365-133">One set of NVAs implements rules for Internet traffic only, while another set of NVAs implement rules for on-premises traffic only.</span></span>

<span data-ttu-id="0e365-134">Incluya una aplicación virtual de red de capa 7 para terminar las conexiones de aplicación en el nivel de NVA y mantener la compatibilidad con los niveles de back-end.</span><span class="sxs-lookup"><span data-stu-id="0e365-134">Include a layer-7 NVA to terminate application connections at the NVA level and maintain compatibility with the backend tiers.</span></span> <span data-ttu-id="0e365-135">Así se garantiza una conectividad simétrica donde el tráfico de respuesta de los niveles de back-end se devuelve a través de la aplicación virtual de red.</span><span class="sxs-lookup"><span data-stu-id="0e365-135">This guarantees symmetric connectivity where response traffic from the backend tiers returns through the NVA.</span></span>  

### <a name="public-load-balancer-recommendations"></a><span data-ttu-id="0e365-136">Recomendaciones para el equilibrador de carga público</span><span class="sxs-lookup"><span data-stu-id="0e365-136">Public load balancer recommendations</span></span>

<span data-ttu-id="0e365-137">Para conseguir escalabilidad y disponibilidad, implemente las aplicaciones virtuales de red de la red perimetral en un [conjunto de disponibilidad][availability-set] y use un [equilibrador de carga accesible desde Internet][load-balancer] para distribuir las solicitudes de Internet entre las aplicaciones virtuales de red de dicho conjunto.</span><span class="sxs-lookup"><span data-stu-id="0e365-137">For scalability and availability, deploy the public DMZ NVAs in an [availability set][availability-set] and use an [Internet facing load balancer][load-balancer] to distribute Internet requests across the NVAs in the availability set.</span></span>  

<span data-ttu-id="0e365-138">Configure el equilibrador de carga para que solo acepte solicitudes en los puertos necesarios con el tráfico de Internet.</span><span class="sxs-lookup"><span data-stu-id="0e365-138">Configure the load balancer to accept requests only on the ports necessary for Internet traffic.</span></span> <span data-ttu-id="0e365-139">Por ejemplo, limite las solicitudes HTTP entrantes al puerto 80 y las solicitudes HTTPS entrantes al puerto 443.</span><span class="sxs-lookup"><span data-stu-id="0e365-139">For example, restrict inbound HTTP requests to port 80 and inbound HTTPS requests to port 443.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="0e365-140">Consideraciones sobre escalabilidad</span><span class="sxs-lookup"><span data-stu-id="0e365-140">Scalability considerations</span></span>

<span data-ttu-id="0e365-141">Aunque la arquitectura requiera inicialmente una única aplicación virtual de red en la red perimetral pública, se recomienda colocar un equilibrador de carga delante de la red perimetral pública desde el principio.</span><span class="sxs-lookup"><span data-stu-id="0e365-141">Even if your architecture initially requires a single NVA in the public DMZ, we recommend putting a load balancer in front of the public DMZ from the beginning.</span></span> <span data-ttu-id="0e365-142">De esta manera, será más sencillo escalar a varias aplicaciones virtuales de red si es necesario en el futuro.</span><span class="sxs-lookup"><span data-stu-id="0e365-142">That will make it easier to scale to multiple NVAs in the future, if needed.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="0e365-143">Consideraciones sobre disponibilidad</span><span class="sxs-lookup"><span data-stu-id="0e365-143">Availability considerations</span></span>

<span data-ttu-id="0e365-144">El equilibrador de carga accesible desde Internet necesita cada una de las aplicaciones virtuales de red de la subred de entrada de la red perimetral pública para implementar un [sondeo de estado][lb-probe].</span><span class="sxs-lookup"><span data-stu-id="0e365-144">The Internet facing load balancer requires each NVA in the public DMZ inbound subnet to implement a [health probe][lb-probe].</span></span> <span data-ttu-id="0e365-145">Un sondeo de estado que no responde en este punto de conexión se considera que no disponible y el equilibrador de carga dirigirá las solicitudes a otras aplicaciones virtuales de red del mismo conjunto de disponibilidad.</span><span class="sxs-lookup"><span data-stu-id="0e365-145">A health probe that fails to respond on this endpoint is considered to be unavailable, and the load balancer will direct requests to other NVAs in the same availability set.</span></span> <span data-ttu-id="0e365-146">Tenga en cuenta que si ninguna aplicación virtual de red responde, la aplicación dará error, así que es importante tener configurada la supervisión para que DevOps reviva el aviso cuando el número de instancias de NVA en buen estado descienda por debajo de un umbral definido.</span><span class="sxs-lookup"><span data-stu-id="0e365-146">Note that if all NVAs fail to respond, your application will fail, so it's important to have monitoring configured to alert DevOps when the number of healthy NVA instances falls below a defined threshold.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="0e365-147">Consideraciones sobre la manejabilidad</span><span class="sxs-lookup"><span data-stu-id="0e365-147">Manageability considerations</span></span>

<span data-ttu-id="0e365-148">Toda la supervisión y administración de las aplicaciones virtuales de red de la red perimetral pública se debe realizar en el Jumpbox en la subred de administración.</span><span class="sxs-lookup"><span data-stu-id="0e365-148">All monitoring and management for the NVAs in the public DMZ should be be performed by the jumpbox in the management subnet.</span></span> <span data-ttu-id="0e365-149">Como se describe en [Implementación de una red perimetral entre Azure y el centro de datos local][implementing-a-secure-hybrid-network-architecture], defina una ruta de red única entre la red local y el Jumpbox que pase por la puerta de enlace, a fin de restringir acceso.</span><span class="sxs-lookup"><span data-stu-id="0e365-149">As discussed in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture], define a single network route from the on-premises network through the gateway to the jumpbox, in order to restrict access.</span></span>

<span data-ttu-id="0e365-150">Aunque la conectividad de puerta de enlace de la red local a Azure esté fuera de servicio, puede comunicarse con el Jumpbox; para ello, implemente una dirección IP pública, agréguela al JumpBox e inicie sesión desde Internet.</span><span class="sxs-lookup"><span data-stu-id="0e365-150">If gateway connectivity from your on-premises network to Azure is down, you can still reach the jumpbox by deploying a public IP address, adding it to the jumpbox, and logging in from the Internet.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="0e365-151">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="0e365-151">Security considerations</span></span>

<span data-ttu-id="0e365-152">Esta arquitectura de referencia implementa varios niveles de seguridad:</span><span class="sxs-lookup"><span data-stu-id="0e365-152">This reference architecture implements multiple levels of security:</span></span>

* <span data-ttu-id="0e365-153">El equilibrador de carga accesible desde Internet dirige las solicitudes a las aplicaciones virtuales de red de la subred de la red experimental pública de entrada, y solo en los puertos necesarios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0e365-153">The Internet facing load balancer directs requests to the NVAs in the inbound public DMZ subnet, and only on the ports necessary for the application.</span></span>
* <span data-ttu-id="0e365-154">Las reglas NSG de las subredes de la red perimetral pública de entrada y salida impiden que las aplicaciones virtuales de red se pongan en peligro, ya que bloquean las solicitudes que se encuentran fuera de estas reglas.</span><span class="sxs-lookup"><span data-stu-id="0e365-154">The NSG rules for the inbound and outbound public DMZ subnets prevent the NVAs from being compromised, by blocking requests that fall outside of the NSG rules.</span></span>
* <span data-ttu-id="0e365-155">La configuración de enrutamiento NAT de las aplicaciones virtuales de red dirige las solicitudes entrantes en los puertos 80 y 443 al equilibrador de carga de nivel web, pero omite las solicitudes en todos los demás puertos.</span><span class="sxs-lookup"><span data-stu-id="0e365-155">The NAT routing configuration for the NVAs directs incoming requests on port 80 and port 443 to the web tier load balancer, but ignores requests on all other ports.</span></span>

<span data-ttu-id="0e365-156">Todas las solicitudes entrantes se deben registrar en todos los puertos.</span><span class="sxs-lookup"><span data-stu-id="0e365-156">You should log all incoming requests on all ports.</span></span> <span data-ttu-id="0e365-157">Realice auditorías de los registros con regularidad y preste atención a las solicitudes que se encuentran fuera de los parámetros previstos, ya que podrían ser indicio de intentos de intrusión.</span><span class="sxs-lookup"><span data-stu-id="0e365-157">Regularly audit the logs, paying attention to requests that fall outside of expected parameters, as these may indicate intrusion attempts.</span></span>

## <a name="solution-deployment"></a><span data-ttu-id="0e365-158">Implementación de la solución</span><span class="sxs-lookup"><span data-stu-id="0e365-158">Solution deployment</span></span>

<span data-ttu-id="0e365-159">Se puede encontrar una implementación de una arquitectura de referencia que implementa estas recomendaciones en [GitHub][github-folder].</span><span class="sxs-lookup"><span data-stu-id="0e365-159">A deployment for a reference architecture that implements these recommendations is available on [GitHub][github-folder].</span></span> <span data-ttu-id="0e365-160">La arquitectura de referencia se puede implementar con máquinas virtuales Windows o Linux siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="0e365-160">The reference architecture can be deployed either with Windows or Linux VMs by following the directions below:</span></span>

1. <span data-ttu-id="0e365-161">Haga clic en el botón a continuación:</span><span class="sxs-lookup"><span data-stu-id="0e365-161">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2FvirtualNetwork.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="0e365-162">Una vez abierto el vínculo en Azure Portal, debe especificar los valores de algunas de las opciones:</span><span class="sxs-lookup"><span data-stu-id="0e365-162">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>
   * <span data-ttu-id="0e365-163">El nombre del **Grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-public-dmz-network-rg` en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="0e365-163">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-public-dmz-network-rg` in the text box.</span></span>
   * <span data-ttu-id="0e365-164">Seleccione la región en el cuadro de lista desplegable **Ubicación**.</span><span class="sxs-lookup"><span data-stu-id="0e365-164">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="0e365-165">No modifique los cuadros de texto **URI raíz de plantilla** o **URI raíz de parámetro**.</span><span class="sxs-lookup"><span data-stu-id="0e365-165">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="0e365-166">Seleccione el **tipo de SO** en el cuadro de lista desplegable: **windows** o **linux**.</span><span class="sxs-lookup"><span data-stu-id="0e365-166">Select the **Os Type** from the drop down box, **windows** or **linux**.</span></span>
   * <span data-ttu-id="0e365-167">Revise los términos y condiciones, y haga clic en la casilla **Acepto los términos y condiciones indicados anteriormente**.</span><span class="sxs-lookup"><span data-stu-id="0e365-167">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="0e365-168">Haga clic en el botón **Comprar**.</span><span class="sxs-lookup"><span data-stu-id="0e365-168">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="0e365-169">Espere a que la implementación se complete.</span><span class="sxs-lookup"><span data-stu-id="0e365-169">Wait for the deployment to complete.</span></span>
4. <span data-ttu-id="0e365-170">Haga clic en el botón a continuación:</span><span class="sxs-lookup"><span data-stu-id="0e365-170">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fworkload.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. <span data-ttu-id="0e365-171">Una vez abierto el vínculo en Azure Portal, debe especificar los valores de algunas de las opciones:</span><span class="sxs-lookup"><span data-stu-id="0e365-171">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>
   * <span data-ttu-id="0e365-172">El nombre del **Grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-public-dmz-wl-rg` en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="0e365-172">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-public-dmz-wl-rg` in the text box.</span></span>
   * <span data-ttu-id="0e365-173">Seleccione la región en el cuadro de lista desplegable **Ubicación**.</span><span class="sxs-lookup"><span data-stu-id="0e365-173">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="0e365-174">No modifique los cuadros de texto **URI raíz de plantilla** o **URI raíz de parámetro**.</span><span class="sxs-lookup"><span data-stu-id="0e365-174">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="0e365-175">Revise los términos y condiciones, y haga clic en la casilla **Acepto los términos y condiciones indicados anteriormente**.</span><span class="sxs-lookup"><span data-stu-id="0e365-175">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="0e365-176">Haga clic en el botón **Comprar**.</span><span class="sxs-lookup"><span data-stu-id="0e365-176">Click the **Purchase** button.</span></span>
6. <span data-ttu-id="0e365-177">Espere a que la implementación se complete.</span><span class="sxs-lookup"><span data-stu-id="0e365-177">Wait for the deployment to complete.</span></span>
7. <span data-ttu-id="0e365-178">Haga clic en el botón a continuación:</span><span class="sxs-lookup"><span data-stu-id="0e365-178">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fsecurity.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
8. <span data-ttu-id="0e365-179">Una vez abierto el vínculo en Azure Portal, debe especificar los valores de algunas de las opciones:</span><span class="sxs-lookup"><span data-stu-id="0e365-179">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>
   * <span data-ttu-id="0e365-180">El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Usar existente** y escriba `ra-public-dmz-network-rg` en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="0e365-180">The **Resource group** name is already defined in the parameter file, so select **Use Existing** and enter `ra-public-dmz-network-rg` in the text box.</span></span>
   * <span data-ttu-id="0e365-181">Seleccione la región en el cuadro de lista desplegable **Ubicación**.</span><span class="sxs-lookup"><span data-stu-id="0e365-181">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="0e365-182">No modifique los cuadros de texto **URI raíz de plantilla** o **URI raíz de parámetro**.</span><span class="sxs-lookup"><span data-stu-id="0e365-182">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="0e365-183">Revise los términos y condiciones, y haga clic en la casilla **Acepto los términos y condiciones indicados anteriormente**.</span><span class="sxs-lookup"><span data-stu-id="0e365-183">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="0e365-184">Haga clic en el botón **Comprar**.</span><span class="sxs-lookup"><span data-stu-id="0e365-184">Click the **Purchase** button.</span></span>
9. <span data-ttu-id="0e365-185">Espere a que la implementación se complete.</span><span class="sxs-lookup"><span data-stu-id="0e365-185">Wait for the deployment to complete.</span></span>
10. <span data-ttu-id="0e365-186">Los archivos de parámetros incluyen un nombre de usuario y una contraseña de administrador codificados de forma rígida, y es muy recomendable cambiarlos inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="0e365-186">The parameter files include hard-coded administrator user name and password for all VMs, and it is strongly recommended that you immediately change both.</span></span> <span data-ttu-id="0e365-187">Seleccione cada máquina virtual de la implementación en Azure Portal y haga clic en **Restablecer contraseña** en la hoja **Soporte técnico y solución de problemas**.</span><span class="sxs-lookup"><span data-stu-id="0e365-187">For each VM in the deployment, select it in the Azure portal and then click  **Reset password** in the **Support + troubleshooting** blade.</span></span> <span data-ttu-id="0e365-188">Seleccione **Restablecer contraseña** en el cuadro de lista desplegable **Modo**, seleccione un valor de **Nombre de usuario** y un valor de **Contraseña**.</span><span class="sxs-lookup"><span data-stu-id="0e365-188">Select **Reset password** in the **Mode** drop down box, then select a new **User name** and **Password**.</span></span> <span data-ttu-id="0e365-189">Haga clic en el botón **Actualizar** para guardar.</span><span class="sxs-lookup"><span data-stu-id="0e365-189">Click the **Update** button to save.</span></span>


[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-dmz

[implementing-a-secure-hybrid-network-architecture]: ./secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview
[load-balancer]: /azure/load-balancer/load-balancer-Internet-overview
[network-security-group]: /azure/virtual-network/virtual-networks-nsg

[visio-download]: https://archcenter.azureedge.net/cdn/dmz-reference-architectures.vsdx


[0]: ./images/dmz-public.png "Arquitectura de red híbrida segura"