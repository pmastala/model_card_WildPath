# Model Card for WildPath Zoonotic Risk Map

**Scenario:** WildPath: Zoonotic Risk Map for Ecotourism Travelers
**Authors:** Precious Innocent Mastala **Date:** 1st April 2026

------------------------------------------------------------------------

## 1. Model Details

**Model type:** Spatiotemporal random forest (ensemble learning;
classification + regression hybrid)

**Task:** Zoonotic disease risk estimation at subnational spatial
resolution (province, district, or park/reserve level), updated on a
weekly automated cycle.

**Developed by:** WildPath (a commercial ecotourism health technology
company). The development team includes data scientists, geospatial
analysts, and infectious disease consultants.

**Version:** Continuous deployment model — risk estimates update weekly,
implying a rolling versioning architecture. No discrete version numbers
are published to users but includes date updated.

**Development period:** The model was originally developed between 2017
and 2020, with input refreshes (but not full retraining) continuing to
the present.

**Input features:** 
- Wildlife biodiversity data: species distributions
and known pathogen-host associations (IUCN Red List, GBIF occurrence records)
- Historical outbreak records: WHO Disease Outbreak News,
ProMED-mail alerts, local health ministry reports 
- Environmental and climatic variables: temperature, precipitation, land-use change indices,
and deforestation proximity metrics (likely sourced from MODIS land
cover, ERA5 climate reanalysis, and Global Forest Watch)
- Real-time participatory surveillance: traveler-reported symptom data via in-app
module (\~12,000 active contributors) 
- Travel medicine advisories: CDC
Travelers' Health standing guidance and WHO International Travel and
Health recommendations

**Output:** 
- A categorical risk tier (Low / Moderate / High / Very
High) per subnational unit 
- Activity-specific risk modifiers (e.g., bat cave entry, open grassland
safari, marine wildlife encounters) 
- Personalized pre-travel recommendations covering vaccinations and
prophylaxis

------------------------------------------------------------------------

## 2. Intended Use

**Primary task:** Estimate relative zoonotic disease transmission risk
at ecotourism destinations to inform pre-travel health planning
decisions for international leisure travelers.

**Intended users:** 
- International ecotourists planning wildlife-focused travel (safaris, jungle treks,
birdwatching, cave exploration, marine wildlife encounters) 
- Travel agencies and tour operators using WildPath as a destination health planning tool 
- Premium subscribers receiving personalized health briefings and post-travel
symptom monitoring

**Decisions this system informs:** 
- Whether to seek pre-travel vaccines or prophylactic medications before departure 
- Which specific activities to pursue or avoid at a destination 
- Whether to proceed with travel to a given region at all 
- Which travel insurance or medical coverage to purchase

**Regulatory status & Cautionary note:** 
Not a registered medical device. Has not undergone clinical validation by any regulatory
authority (FDA, EMA, or equivalent). Vaccine and prophylaxis
recommendations are generated algorithmically, not by licensed
clinicians.

**Deployment context:** Consumer-facing web and mobile application,
available in English, French, Spanish, and Mandarin. Users interact with
an interactive map and receive a personalized risk summary based on
destination, travel dates, and planned activities. The system operates
in a lay health communication context — most users are not clinicians
and will interpret risk tiers at face value, without the epidemiological
literacy to interrogate the underlying data quality.

------------------------------------------------------------------------

## 3. Out-of-Scope Uses

**This system should NOT be used for:**

-   **Clinical diagnosis or treatment decisions.** WildPath is not a
    diagnostic tool. It cannot determine whether a traveler is currently
    infected, and its output should never substitute for clinical
    evaluation of symptoms before or after travel.

-   **Surveillance-grade epidemiological reporting.** Risk estimates are
    derived in part from participatory self-reported data and secondary
    aggregations of historical records. They do not meet the evidentiary
    standards required for national or international public health
    surveillance and should not be cited as such.

-   **Real-time outbreak response.** The weekly update cycle cannot keep
    pace with acute outbreak events. If a novel spillover is confirmed
    at a popular ecotourism site on a Monday, the model will not reflect
    elevated risk until the following week's update at the earliest —
    during which time travelers may be actively booking or arriving.

-   **Post-exposure management.** A traveler who has experienced a
    potential exposure event (animal bite, bat encounter, contact with
    sick wildlife) requires immediate clinical consultation, not a risk
    tier check. The app should route these users to travel medicine
    services, not reassure them with a pre-trip risk score.

-   **Occupational wildlife exposure settings.** Rangers, wildlife
    biologists, field veterinarians, and ecologists face different
    exposure patterns, frequencies, and routes than leisure tourists.
    WildPath was calibrated on tourist behavior; its activity modifiers
    do not translate to occupational contexts.

**Foreseeable misuses:**
- Travelers using a "Low" designation to
justify skipping recommended pre-travel vaccines or chemoprophylaxis,
not realizing the designation may reflect surveillance absence rather
than genuine low hazard.
- Tour operators marketing destinations using a
favorable WildPath risk tier as a safety assurance in promotional
materials, without disclosing the tool's data limitations.
- Users in
under-surveilled regions treating artificially low scores as
confirmation that the destination is safe, when the score may simply
reflect that no one has been looking.

------------------------------------------------------------------------

## 4. Training Data

**Wildlife biodiversity data (IUCN Red List / GBIF):** Global species
distribution records and pathogen-host association databases. Coverage
is strong for well-studied vertebrate groups (large mammals, birds) but
sparse for the taxa most epidemiologically relevant to zoonotic
transmission: bats (reservoirs for henipaviruses, filoviruses, and
coronaviruses), rodents (arenaviruses, hantaviruses), and arthropod
vectors (ticks, mosquitoes, sandflies). GBIF records are particularly
incomplete in Central Africa, interior South America, and Southeast
Asian island chains — exactly the biodiversity-rich, high-spillover-risk
regions WildPath purports to cover. This creates a systematic blind
spot: the model may underweight risk from the reservoir groups most
likely to drive novel spillovers precisely because observation effort
for those groups is lowest.

**Historical outbreak data (WHO DONs, ProMED, health ministry reports):**
Outbreak records are inherently biased toward regions with
functional surveillance infrastructure, laboratory capacity, and
political will to report. The model treats data absence as signal,
interpreting unmonitored regions as lower risk. This is a structural
error, not a gap that more data will automatically fix: regions with the
most sparse reporting are also the regions least likely to generate
future training data.

**Environmental and climatic variables:** Drawn from publicly available
remote sensing datasets like MODIS land cover classifications, ERA5
climate reanalysis, and Global Forest Watch deforestation alerts. These
products have weekly-to-monthly update cycles and global coverage.
However, fine-scale deforestation (clearing of less than a few hectares)
relevant to local spillover risk at wildlife-human interfaces may not be
captured. Seasonal variation in vector breeding habitat — a key driver
of Dengue, Rift Valley Fever, and other arboviruses — may be smoothed
over by the weekly aggregation cycle.

**Participatory surveillance (\~12,000 active users):**
Traveler-reported symptom data are geographically concentrated in
high-traffic ecotourism destinations: East Africa, Southeast Asia, and
Costa Rica. For the vast majority of zoonotic-rich but less-visited
regions, this data stream contributes effectively nothing to the model.
The user base is self-selected — health-conscious, tech-literate
international travelers — which makes the sample systematically
different from the broader traveling population. Critically, symptoms
are unverified and unspecific sometimes and the app has no way to
distinguish. Participatory data thus adds noise at low user densities
and directionally biased signal at high user densities.

**Time period covered:** The dataset spans from the 2000 to present.

------------------------------------------------------------------------

## 5. Evaluation Data & Metrics

**What evaluation should include:**

**Discrimination:** AUC-ROC and AUC-PRC (precision-recall curve)
reported separately for each risk tier (Low, Moderate, High, Very High).
Because risk tiers are ordered and the consequences of misclassification
are asymmetric, weighted metrics — penalizing missed High-risk
designations more heavily than false positives — are more appropriate
than raw accuracy.

**Calibration:** Calibration plots (reliability diagrams) comparing
predicted risk tier frequency to actual observed outbreak incidence in
held-out subnational units over the evaluation period.

**Sensitivity and specificity by tier:** Given the asymmetric real-world
consequences — a missed High-risk designation means unvaccinated
travelers entering a hazardous area; a false Very High designation means
economic harm to ecotourism-dependent communities — sensitivity and
specificity are reported separately for each tier transition (e.g., High
vs. not-High), not collapsed into a single accuracy figure.

**Temporal validation:** The model was be tested using a time-held-out
dataset thats is trained exclusively on data from 2000–2018 and
evaluated against 2018–2020 outbreak records — to assess whether
accuracy degrades as the temporal gap between training data and
deployment widens.

------------------------------------------------------------------------

## 6. Quantitative Analysis

The following stratifications are essential for public health evaluation
and were completed before the system was deployed.

**By geography and surveillance infrastructure:** Performance almost
certainly degrades in under-surveilled regions. A model calibrated on
data-rich environments (Thailand, Kenya, Costa Rica) and applied to
data-sparse environments (interior DRC, Laos, Papua New Guinea) is
extrapolating outside its training distribution. Accuracy metrics should
be reported in three tiers: high data density regions, medium density,
and low density — with transparent definitions of how density is
measured.

**By disease class:** Risk estimates for diseases with decades of dense
surveillance data (malaria, dengue, rabies, leptospirosis) are likely
more reliable than estimates for emerging or rare pathogens
(henipaviruses, filoviruses, novel coronaviruses) where the model has
few positive training examples. Performance by pathogen class should be
reported separately so users can understand where the model is operating
on solid ground versus extrapolating.

**By activity type:** Activity-specific risk modifiers (bat cave, open
grassland, marine encounter) should each have independent validation. A
modifier derived from a handful of documented superspreading events may
produce confident-looking risk adjustments that do not generalize across
geographic contexts. Bat caves in Southeast Asia and bat caves in West
Africa have different species compositions, different occupancy
patterns, and different associated pathogens.

**By season:** Many zoonotic diseases are strongly seasonal — Rift
Valley Fever follows rainfall, dengue tracks Aedes mosquito breeding
cycles, rodent-associated hantaviruses peak after harvest. The model's
accuracy should be stratified by season of travel to determine whether
risk tiers remain valid across the year or require seasonal
recalibration.

**Known directional biases:**

| Subgroup | Expected bias direction | Mechanism |
|------------------------|------------------------|------------------------|
| Under-surveilled regions (LMICs) | Systematic underestimation | Data absence read as low risk |
| High-media-profile outbreak sites | Potential overestimation | Reporting intensity after amplified events inflates training signal |
| Bat and rodent reservoir zones | Underestimation | Sparse IUCN/GBIF coverage of key taxa |
| Dry season travel | Possible underestimation for some arboviruses | Seasonal spillover dynamics not fully captured |
| Less popular ecotourism destinations | High uncertainty, direction unclear | Near-zero participatory surveillance signal |

------------------------------------------------------------------------

## 7. Ethical Considerations

**Surveillance bias as a structural equity problem:** The model's most
consequential limitation (conflating data absence with low risk) is
not a neutral technical imperfection. It systematically assigns
artificially safe scores to regions where public health infrastructure
is weakest, which are disproportionately low- and middle-income
countries. Wealthy international travelers receive false reassurance. If
those travelers arrive unvaccinated and become infected, the burden
falls on local health systems that are already under-resourced: they
consume clinical care, contribute to local transmission chains, and
generate outbreak investigation costs they did not cause. The model's
design effectively transfers health risk from the traveler to the
destination community.

**Economic harm from overcautious designations:** A "Very High"
designation for a specific park or district can suppress tourism to
communities whose livelihoods depend on ecotourism revenue. If that
designation reflects a historical, media-amplified outbreak from years
prior rather than current epidemiological conditions, the economic harm
to local communities is real and ongoing — and it is not corrected by
the next weekly model update. WildPath should publish a formal process
by which destination communities, national health authorities, or park
management agencies can contest a risk designation, submit updated local
data, and request expedited review. Without this, the model's errors are
unappealable by those most affected.

**Informed consent for participatory health surveillance:** The app
collects symptom reports from 12,000+ users who are, in effect,
contributing to a commercial health surveillance system. Key questions
that should be answered in the app's terms and data policy, and are not
addressed in the scenario: Are users explicitly informed that their
symptom data contributes to the risk model? What personal data is
retained alongside symptom reports (GPS location, device ID, travel
itinerary)? For how long is this data stored, and can users request
deletion? Post-travel symptom monitoring in the premium tier is
particularly sensitive — a user flagged as symptomatic post-travel from
a high-risk zone may have a reportable condition, and it is unclear
whether WildPath has any obligation or mechanism to notify public health
authorities.

**Data sovereignty and extractive data practices:** Wildlife
biodiversity records, outbreak data, and environmental datasets from
low-income countries underpin WildPath's commercial product. The
communities and governments whose ecological and epidemiological data
feeds the model receive no documented benefit, have no governance role
in how that data is used, and may not be aware that their national
surveillance records are powering a subscription product sold to
international tourists. This mirrors broader critiques of extractive
data practices in global health AI — where data flows from the Global
South to inform products consumed in the Global North, without
reciprocal benefit or consent.

**Liability and the clinical guidance gap:** When WildPath recommends
specific vaccines or prophylactic drug regimens, it is generating
medical guidance without clinical licensure, without knowledge of the
user's health history, contraindications, or current medications, and
without any recourse mechanism if the recommendation is wrong. The "not
a medical device" disclaimer limits legal liability; it does not limit
the real-world harm. A pregnant traveler who receives a generic
prophylaxis recommendation that is contraindicated in pregnancy —
because WildPath has no way to know she is pregnant — faces genuine
clinical risk. The ethical obligation is to design for safe failure:
prominently flag the limits of algorithmic medical guidance and
consistently direct users toward licensed clinical consultation before
acting on vaccine or drug recommendations.

**Asymmetric consequence-bearing:** The travelers who act on incorrect
guidance bear the health consequences. The ecotourism-dependent
communities affected by overcautious or undercautious designations bear
the economic and epidemiological consequences. WildPath's developers and
investors bear neither. This asymmetry is not unique to WildPath — it
characterizes much of consumer health AI — but it should be named
explicitly and should inform how the system communicates uncertainty.
When the model is operating on thin data, users should be told so, in
plain language, at the point of decision.

------------------------------------------------------------------------

## 8. Limitations & Caveats

**Surveillance bias (most critical):** Absence of documented outbreaks
does not mean absence of risk. A "Low" designation in a region with no
surveillance infrastructure reflects the limits of the data, not the
limits of the hazard. This is the single most important limitation and
must be communicated to users at the point of consumption — in the
interface, at the moment they view a risk tier — not only in a
terms-of-service document no one reads. Suggested language for the app:
*"This region has limited historical disease monitoring data. A Low
rating here means we have fewer reports, not necessarily that the risk
is lower than comparable destinations."*

**Weekly update cycle and acute event blindness:** A confirmed Marburg
cluster in a wildlife corridor, a newly identified bat coronavirus
spillover at a cave tourism site, or a Lassa fever outbreak at a forest
ecolodge will not be reflected in WildPath's risk tier for up to seven
days after detection — if the model picks up the signal at all. The app
must integrate a real-time alert layer (linked to CDC Health Notices and
WHO DONs) that can override the map tier immediately for active outbreak
events, independent of the weekly update cycle.

**Taxonomic blind spot in biodiversity data:** Bats and rodents — the
most important reservoir taxa for emerging zoonoses — are the groups
most poorly represented in IUCN and GBIF records. The model's
biodiversity signal is strongest for the organisms least implicated in
novel spillovers. A region may receive a lower risk score partly because
bat species diversity there is poorly documented, not because fewer bat
species are present.

**Participatory data: self-selection, geographic concentration, and
unconfirmed symptoms:** 12,000 active users is a small, non-random,
geographically concentrated sample. Symptom reports are self-assessed,
not clinically confirmed, and concentrated in destinations that already
have the most formal outbreak data. This means the participatory stream
adds the most value precisely where it is least needed (well- monitored
destinations) and contributes almost nothing where it would be most
valuable (data-sparse regions). The stream should be treated as a weak,
supplementary signal, not a real-time surveillance substitute.

**Activity modifiers are ecologically coarse:** "Bat cave" as a risk
category does not differentiate between a seasonally unoccupied cave in
a temperate zone and a maternity roost of hundreds of thousands of
Rhinolophus bats in tropical Asia during pupping season. The exposure
risk between these two scenarios differs by orders of magnitude.
Activity modifiers should be accompanied by explicit confidence
intervals and should note when they are derived from a small number of
documented events rather than systematic ecological data.

**Population-level, not individual-level, risk:** The model outputs a
geographic risk estimate — the probability that someone in this location
at this time encounters an infectious agent. It says nothing about
individual susceptibility. An immunocompromised traveler, a pregnant
traveler, a traveler on immunosuppressive therapy, or a child face
substantially different risk profiles under the same geographic
estimate. The same "Moderate" designation is appropriate guidance for
different people in very different ways, and the app has no mechanism to
account for this.

**Evidence base is primarily English-language:** Outbreak records,
advisories, and peer-reviewed literature incorporated into the model are
predominantly indexed in English-language databases. Disease clusters
documented in Portuguese, French, Swahili, or Bahasa language health
ministry reports, or published in regional journals not indexed by
ProMED or PubMed, may not be captured. This adds a language-of-science
bias to the existing surveillance infrastructure bias.

------------------------------------------------------------------------

## 9. Human Oversight

**Current oversight level: Automated, human out of the loop for
individual risk designations.**

The spatiotemporal random forest generates and publishes risk estimates
on a weekly automated cycle. No documented process exists by which a
clinician, epidemiologist, or national health authority reviews or
approves individual subnational risk tier changes before they are
published.

**What currently exists:** - CDC and WHO travel advisories are
incorporated as model inputs, providing indirect expert influence on
risk estimates — but this is ex ante influence on training, not ex post
review of individual outputs - Premium users receive "personalized
health briefings," implying some human-authored content — but the
credentials, clinical authority, and review process for those briefings
are not disclosed - The app provides links to CDC and WHO advisories,
giving users a path to authoritative human-reviewed guidance alongside
the algorithmic output

**What must be built before expanded deployment:**

*Mandatory review for tier upgrades:* Any subnational unit moving from a
lower to a higher risk tier (e.g., Moderate → High, High → Very High)
should require review and sign-off by a qualified epidemiologist or
travel medicine physician before the change is published. Tier
downgrades — which affect economic interests of destination communities
— should be subject to the same review.

*Emergency override protocol:* When a WHO DON or CDC Level 2/3 advisory
is issued for a destination currently rated Low or Moderate in WildPath,
the app needs a documented emergency protocol: who has authority to
issue an immediate override, within what timeframe (hours, not days),
and how is the override communicated to users who have already
downloaded a risk summary for that destination.

*User escalation pathway:* Users who report a potential exposure event
(animal bite, bat encounter, handling of sick wildlife) through the app
should be immediately routed to a clinical resource — a travel medicine
helpline, country-specific emergency contacts, or telehealth — not left
with a pre-trip risk score as their only guidance. The app should not be
the last stop in a post-exposure workflow.

*Community contestation mechanism:* Local health authorities, national
park agencies, and destination community representatives should have a
formal channel to flag a risk designation they believe is inaccurate,
provide supporting local data, and receive a response within a defined
timeframe. Human review of these submissions should be mandatory, not
optional.

*Boundaries on autonomous action:* The system currently displays
information; it does not take action. It should not be extended to take
autonomous action — notifying insurers, flagging users to border health
authorities, canceling bookings — without a fundamentally different
oversight architecture and explicit regulatory review.

------------------------------------------------------------------------

## 10. Temporal Validity

**Current update cadence:** Input data (participatory surveillance,
advisory documents) refreshes weekly with periodic retraining of the underlying random
forest model.

**Expected performance shelf life by region type:**

| Region type | Expected shelf life of risk designation | Key degradation driver |
|------------------------|------------------------|------------------------|
| Stable endemic disease zones (e.g., holoendemic malaria) | 12–24 months | Slow-changing |
| Active deforestation frontiers | 3–6 months | Habitat loss alters spillover interfaces rapidly |
| Regions with recent novel spillover events | Immediate reassessment needed | Model has no prior training signal |
| High-participatory-data destinations | 6–12 months | Surveillance data may detect drift |
| Under-surveilled regions | Indeterminate — degradation undetectable | No signal to indicate when estimate is stale |

**Triggers that should mandate immediate review or retraining:**

-   **Novel pathogen spillover** at any ecotourism-accessible location:
    the model has no training signal for a new pathogen and its risk
    estimate for that region is uninformative until retrained on
    post-spillover data
-   **WHO DON or CDC Level 2+ advisory** issued for a destination the
    model currently rates Low or Moderate: the advisory represents human
    expert judgment that supersedes the model's estimate and should
    trigger an immediate review of the underlying inputs
-   **Land-use change threshold crossed** in a key biodiversity zone:
    Global Forest Watch or equivalent monitoring should trigger
    automatic review when deforestation in a monitored park buffer zone
    exceeds a defined hectarage threshold
-   **Participatory surveillance spike** in a region: an unusual cluster
    of symptom reports from users in a specific destination should
    trigger epidemiological review, not just feed into the next week's
    model update
-   **IUCN or GBIF major data release** updating reservoir species
    distributions: if bat species ranges are substantially revised in a
    region, the biodiversity input layer must be refreshed and the model
    re-evaluated for that geography
-   **Post-pandemic ecotourism pattern shift**: resumption of travel to
    previously dormant destinations shifts the participatory
    surveillance geography and user base, requiring recalibration of the
    model's spatial coverage assumptions

**Minimum review cycle:** Independent external evaluation of spatial
accuracy conducted by an epidemiologist not involved in WildPath's
development occurs annually, using prospective outbreak data
from the prior 12-48 months as the ground truth.

------------------------------------------------------------------------
