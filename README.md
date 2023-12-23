Basin Inflow LSTM: Forecasting Regional Water Inflow
=====

Overview
----
Basin Inflow LSTM is an implementation of a long short-term memory model for forecasting reservoir inflow in the American River Basin located in the Sacramento, California region. The proposal model is composed of stacked LSTM layers, with regularization applied to both (L2, dropout, and recurrent dropout), followed by a densely connected layer. Model application and evaluation uses river basin data with temporal coverage from 2008-2022. Across the watershed and along forks of the river, daily precipitation, temperature, snow water content and depth, and river discharge and stage are employed to predict local reservoir inflow. Data was pre-processed with a 30-day exponential moving average to give recent weather events more weight in the forecast, normalized, and made stationary for training. 14 years of data were separated into three sets: training set (2008-2017), validation set (2018–2020), and test set (2020-2022). The model achieves a lower MAE score (0.036) in inflow prediction than the baseline model. 


Land Acknowledgement
----
The land currently occupied by the Folsom Reservoir is part of the unceded territory and traditional home of the Nisenan people. In spite of centuries of genocide and occupation, the sovereign Nisenan people persist as caretakers of the Sacramento Valley. The Nevada City Rancheria was once a federally recognized reservation of the Nisenan people (Nevada City Rancheria, n.d.). By executive order (1913) President Woodrow Wilson created the rancheria from a land allotment (1887) acquired by Chief Charlie Cully. However, in 1958 Congress passed the California Rancheria Termination Act which ceased the federal recognition of 41 tribes including Nevada City Rancheria. Most rancherias have been restored, but Nevada City Rancheria is still trying to restore their federal status. Through financial and community support the California Heritage: Indigenous Research Project (CHIRP), a nonprofit organization led by the Nevada City Rancheria Tribal Council, works to mimic the federal Indigenous programs designed to support health, education, housing, and economic stability in the absence of federal recognition, while advocating for its restoration (CHIRP, n.d.). 


Background
----
The Department of Interior’s Bureau of Reclamation (BOR) employs river basin observations including streamflow, snowpack, temperature, and precipitation in addition to projected water demand to operate reservoirs in California’s Central Valley Project. Water managers must optimize storage during dry periods while allocating space for flood control. In 2019, BOR approved a water control manual for the Folsom Reservoir north of Sacramento that relies on weather forecasts to make more accurate release decisions and engage the auxiliary spillway for flood management (BOR, 2021). 

### History 

Sacramento and surrounding municipalities exist on a floodplain. The area receives most of its water from precipitation in the Sierra Nevada during a short wet period consisting of five to six Pacific storms on average, with high year-to-year variability (Ingram & Malamud-Roam, 2015). A difference of one to two major storms can ensure a normal versus a dry water year. Water management in the state has proven challenging since its founding. This challenge was evident in the early years of the state, when in late 1861 and the beginning of 1862 unprecedented snowfall followed by a series of warm storms drenched the western Sierra Nevada with four times its annual average rainfall, swelling the American River Basin, eliminating Gold Rush mining settlements (see Figure 1), displacing communities, and submerging Sacramento under ten feet of water (Ingram & Malamud-Roam, 2015). Notably, on January 11, 1862 the *Nevada City Democrat* reported Indigenous residents left the area a week before the floods, recognizing the weather pattern of atmospheric river storms. 

The extensive destruction of the 1861-1862 floods in Sacramento and across the state, led the city to pursue a project of raising the district by ten to fifteen feet shortly after the flooding. During the decade that followed, the American River’s confluence with the Sacramento River was located upstream. The Natoma Water and Mining Company carried out the construction of a dual-purpose dam about a mile and a half above Folsom on the American River to generate hydroelectric power and capture logs drifting downstream from the Sierra (Sacramento Public Library Authority, 2021, pp. 11-38). In 1868, California agreed to grant the company with the labor of imprisoned people in exchange for acreage for what would become the state’s second-oldest prison. By 1893 Folsom Dam was finished, and in 1895 the newly built Folsom Powerhouse allowed Sacramento to emerge as one of the leading cities in the country using commercial hydroelectric power.

#### Legacy of the Gold Rush

The discovery of gold by American and Indigenous workers in the American River during January 1848 commenced a siege of the region which entailed mining practices that would corrode river quality, with silt clogging streams and halting annual salmon runs (Akins & Bauer Jr., 2021), and pervasive efforts to transform the river through the shoring of levees, dredging, the separation of the river from sloughs, and years of damming, resulting in the removal of sediment-induced turbidity that sheltered salmon from predators along with the aquatic bugs, phytoplankton, and zooplankton supporting them (Arax, 2020). Between 1848 and 1860 California and the federal government legalized and funded the disenfranchisement, enslavement, ethnic cleansing, and genocide of Indigenous people throughout the violent colonization of the Gold Rush era, declining the state population of Indigenous people from 150,000 to 30,000 people (Akins & Bauer Jr., 2021). In 2019, California Governor Gavin Newsom issued an apology on behalf of the state for its historical mistreatment, violence, and neglect, and established the Truth and Healing Council to provide Indigenous people a platform to correct the historical record and begin the collaborative healing process. 

**Figure 1**

***Gold Rush Mining Camps on the American River, 1849***

<img width="1167" alt="1849-mining-camps-on-the-american-river" src="https://github.com/lauren-alexandra/basin-inflow-lstm/assets/56773938/bf99dac3-55a3-4fd7-9d85-e6d208b21871">

*Note*. Many of the mining camps above Beal's Bar on the North Fork are beneath the Folsom Reservoir. Adapted from "The Early Years", by Sacramento Public Library Authority, *Images of America: Lower American River*, (pp. 11-38), 2021, Arcadia Publishing.

**Figure 2**

***Sacramento, December 1861***

<img width="734" alt="1861-sacramento-flooded" src="https://github.com/lauren-alexandra/basin-inflow-lstm/assets/56773938/cf959be5-9321-44a1-94f1-2a1d73c90dca">

*Note*. From "The Untamed River", by Sacramento Public Library Authority, *Images of America: Lower American River*, (pp. 109-127), 2021, Arcadia Publishing. 

### Reservoir 

Folsom Reservoir (1956) was constructed by the U.S. Army Corps of Engineers for the purpose of flood damage reduction with a timely completion. The winter of 1955-1956 brought Pacific and Arctic storms which displaced over fifty thousand Californians and flooded hundreds of thousands of acres of farmland; the Folsom Dam was credited with preventing the inundation of Sacramento (Arax, 2020). Another area within California State Parks’ American River District, the Auburn State Recreation Area, was also evaluated as a potential location for a dam that would have regulated downstream flow into the Folsom Reservoir. However, construction of the dam was frequently impeded for decades by concerns around the dam’s design, cost, and purpose. The fight over the proposed dam was acutely chronicled in Auburn State Recreation Area park ranger Jordan Fisher Smith’s (2006) memoir. In 1991, the district’s superintendent advocated for the concept of a “dry dam” which would have flooded the American River canyons after a storm. The following year the U.S. Army Corps of Engineers as well as a flood control agency formed by local governments supported a flood-control Auburn dam. Yet the quickly growing populations of nearby towns in the Fourth Congressional District exerted an opposing pressure on the district’s representative John Doolittle, and by 1995 he committed to sinking any proposal which did not generate power and store water for his district’s population. Finally, in 2008 the fights concluded when the State Water Resources Control Board revoked BOR’s federal water rights, ending the stalled project. Today decisions balancing flood events in the American River Basin, power generation, and local and state water demand must be handled by the Folsom Reservoir and Dam (see Figure 3).

**Figure 3**

***Folsom Dam, 2023***

![folsom-dam-2023](https://github.com/lauren-alexandra/basin-inflow-lstm/assets/56773938/4f7495be-8d0b-45c8-af9a-5c2836c1b160)

*Note*. Picture of Folsom Dam as viewed from Beal’s Point in August 2023.

### Adaptation 

BOR operation in the American River Basin must withstand not only the region’s typical variability, but also shifts in timing and quantity of precipitation in a warming climate. The state’s water year now experiences earlier snowmelt with lower streamflows in the summer when demand is high (East & Grant, 2023). Positioning the reservoir to hold capacity for floods in the wet season while storing requisite supply for summer months will necessitate close observation of upstream flow variation (Naz et al., 2018) and strategic changes in operation policies downstream. 

Over the past 80 years there has been a greater magnitude of wet extremes during the winter season (Zamora-Reyes et al., 2021). Atmospheric river storms are projected to be more extreme under climate change with a significant decrease in snow at higher elevations in the Sierra Nevada (Huang et al., 2020), generating sizable runoff and obstacles for flood management. In 2023, the Public Policy Institute of California released a nonpartisan report through its Water Policy Center regarding what should be the state’s water priorities (Mount et al., 2023). Recommendations for wet-year management were highlighted, and the institute identified five key areas for progress to be made: 

1. Proactive planning at the regional level before the onset of a wet year stipulates the integration of water supply and flood management.
2. Flooding often occurs disproportionately in low-income and rural communities. Federal, state, and local agencies must consider equity in all aspects of wet-year planning. Additionally, recharge programs can be implemented to improve community drinking water quality.
3. An infrastructure investment program should be created to adapt to climate change, with an emphasis on aquifer restoration, flood defenses, and nature-based solutions. 
4. Expanded floodways and seasonal wetlands can be leveraged to contend with ecosystem issues, flood events, and water supply concerns. 
5. Agencies, particularly the State Water Board, the California Department of Water Resources, and the California Department of Fish and Wildlife should produce a wet-year permit management plan that directs actions in high-flow events. 

The state frequently issues executive orders to manage hydroclimate variability, but more substantial planning, practices, regulations, policies, and infrastructure are required to meet current and future water challenges. 

#### References

Akins, D. B., & Bauer Jr., W. J. (2021). *We are the land: A history of Native California*. University of California Press. 

Arax, M. (2020). *The dreamt land: Chasing water and dust across California*. Vintage Books.

Bureau of Reclamation. (n.d.). *Folsom Dam joint federal project*. https://www.usbr.gov/mp/mpr-news/docs/factsheets/folsom-dam.pdf 

Bureau of Reclamation. (2021). *Water reliability in the west - 2021 SECURE Water Act report*. https://www.usbr.gov/climate/secure/docs/2021secure/2021SECUREReport.pdf

California Heritage: Indigenous Research Project. (n.d.). *Mission: To preserve, protect and perpetuate Nisenan culture*. https://chirpca.org

East, A. E., & Grant, G. E. (2023). A watershed moment for western U.S. dams. *Water Resources Research, 59*(10). https://doi.org/10.1029/2023WR035646 

Huang, X., Stevenson, S., & Hall, A. D. (2020). Future warming and intensification of precipitation extremes: A “double whammy” leading to increasing flood risk in California. *Geophysical Research Letters, 47*(16), 1-9. https://doi.org/10.1029/2020GL088679 

Ingram, B. L., & Malamud-Roam, F. (2015). *The west without water: What past floods, droughts, and other climatic clues tell us about tomorrow*. University of California Press. 

Mount, J., Grenier, L., Hanak, E., Peterson, C., Bardeen, S., Cole, S., Gartrell, G., Gray, B., Morales, Z. J., & Sencan, G. (2023). *Priorities for California’s water: Stewarding the wet years*. Public Policy Institute of California. https://www.ppic.org/publication/priorities-for-californias-water

Naz, B. S., Kao, S., Ashfaq, M., Gao, H., Rastogi, D., & Gangrade, S. (2018). Effects of climate change on streamflow extremes and implications for reservoir inflow in the United States. *Journal of Hydrology, 566*, 359-370. https://doi.org/10.1016/j.jhydrol.2017.11.027 

Nevada City Rancheria. (n.d.). *Nevada City Rancheria*. https://www.nevadacityrancheria.org

Sacramento Public Library Authority. (2021). The early years. *Images of America: Lower American River* (pp. 11-38). Arcadia Publishing.

Sacramento Public Library Authority. (2021). The untamed river. *Images of America: Lower American River* (pp. 109-127). Arcadia Publishing.

Smith, J. F. (2006). *Nature noir: A park ranger’s patrol in the Sierra*. Mariner Books. 

State of California. (2019, June 18). *Governor Newsom issues apology to Native Americans for State’s historical wrongdoings, establishes Truth and Healing Council.* https://www.gov.ca.gov/2019/06/18/governor-newsom-issues-apology-to-native-americans-for-states-historical-wrongdoings-establishes-truth-and-healing-council

Zamora-Reyes, D., Black, B., & Trouet, V. (2021). Enhanced winter, spring, and summer hydroclimate variability across California from 1940 to 2019. *International Journal of Climatology, 42*(9). https://doi.org/10.1002/joc.7513 


Data
----

California Data Exchange Center: California Department of Water Resources

- Window: Daily and hourly records from 01-01-2008 to 12-31-2022 
- Source: California Data Exchange Center. (n.d.). *CDEC Webservice JSON and CSV*. https://cdec.water.ca.gov/dynamicapp/wsSensorData

#### American River Watershed Stations
<img width="747" alt="River Basin Stations" src="https://github.com/lauren-alexandra/weather-informed-reservoir-operation/assets/56773938/5bd98c71-6fe8-415e-a904-f72d4373ba35">

Google. (n.d.). [River Basin Stations]. Retrieved November 26, 2023, from https://maps.app.goo.gl/5ACnKE3bT1TMQ15J8


Run locally
----

### Download/Clone Git Repository

    $cd <replace with desired location of project folder>
    $git clone https://github.com/lauren-alexandra/basin-inflow-lstm.git
    $cd basin-inflow-lstm

### Create Environment

    $conda create -n myenv
    $conda activate myenv

### Install required packages

    $conda install pip
    $pip install -r requirements.txt

### Set up Jupyter Notebook Kernel

    $pip install --user ipykernel
    $python -m ipykernel install --user --name=myenv

### Launch Jupyter Notebook

    (in git bash or other conda environment)
    $jupyter notebook
