# Understanding Climate Commitments

2022-05-11 10:00
Asim Hussain
Executive Director & Chairperson @ Green Software Foundation
Green Cloud Advocacy Lead @ Microsoft

Theres a lot of nuance in the climate commitments companies make. This talk should help you propose how to make changes that help to meet those commitments.

## Climate Commitments

Carbon Neutral, Net-Zero, 100% Renewable, 24/7 matching etc.

CO2eq (carbon dioxide equivalent) - There are many greenhouse gasses. We normalise everything into CO2eq.

We need to reduce our CO2eq emissions. There's too much. We have a number of ways to do that:

- Abatement/Elimination - Not emitting carbon.
- Offsets
    - Compensation/Avoidance - Paying someone to not emit carbon
    - Neutralisation/Removal - Taking carbon out of the atmosphere

How people are reducing emissions is extremely important. You won't be able to align or suggest things unless you understand that.

## Paths to Zero

Eliminate emissions by 100%. - This is elimination.

OR

100% of the emissions are neutralised/compensated. - This is the minimum definition of carbon neutral.

OR

90% eliminated and 10% neutralised **permanently** - This is net-zero. No compensations are allowed.

OR

Offset more than 100% of your emissions - This is carbon-negative. Or at least the term Microsoft are using. The other three have standards.

## Measuring

There are a couple of ways to measure software for emissions.

- The Greenhouse Gas (GHG) protocol - Large organisations
- Software Carbon Intensity (SCI) Specification - Focussed on people working on software

## GHG Protocol

Scope 1: Direction emissions - related to on-site fuel combustions
Scope 2: Indirect emissions - Related to generation of purchased energy
Scope 3: Other indirect emissions - All other activities you are engaged in. The whole supply chain, business travel, electricity consumers use when using your products.

Private cloud - Scope 2 and 3
Public cloud - Scope 3
Hybrid cloud - Scope 2 and 3
Front End - Scope 3

This can get very confusing. Is it even possible to calculate a total? Do you know everywhere your software is being used? Do you have the capability to track who is using it? Do you have permission to track who is using it?

## Do totals tell the full story?

Q1: Carbon emissions are 34 tonnes
Q2: Carbon emissions increased to 52 tonnes

What's the point of that metric if you can't make choices? What if it's:

Q1: Emissions are 3.3g per user
Q2: Emissions are 2.9g per user

## SCI

It's a carbon per R - which is a flexible 'functional unit'. Could be users, could be API calls.

`SCI = ((E * I) + M) per R`

E = energy consumer
I = carbon emitted per kWh

Go to grnsft.org/sci to learn more. It's in Alpha. Planning to launch at COP27.

The only way to reduce your score here is through elimination. You can only engineer your way to a better score.

Any net-zero strategy has to include 90% elimination and only the final 10% is neutralised. SCI helps an organisation meet it's net-zero target.

## The Green Software Foundation

A sub-foundation of the Linux Foundation. UBS just joined at week. They represent the software industry and are trying to figure out how to reduce the carbon emissions of our software.

grnsft.org/report/join-us