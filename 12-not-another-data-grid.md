# Oh No! Not Another Data Grid!

2022-05-12 09:00
Carly Richmond
Elastic
Ex-Morgan Stanley

## Hats

Carly wore a lot of hats at MS. Developer (.NET, Axiom), Tech Lead, Scrum Master, People Management, Agile Coach, Web UI Developer, Developer Advocate.

Best hat is as a frontend engineer.

Carly started with Desktop UIs and then started building out web dashboards from there. Speaking to users more, learning new tech, build the first data grids and then starting using various charting libraries and visualisations.

## Victims of own success

Now **everyone** wanted a dashboard. Historically, there aren't as many UI devs as backend/fullstack. So backend engineers learned some new tech and went into building frontend controls.

Wrappers were built to make it easier for them to get up to speed. They also did mentorship plans.

A pretty regular pattern came along. Every screen looked the same. 

1. A set of controls to narrow down the selection at the top. Usually multi-select dropdowns or auto-completes.
2. **At least** one data-grid.
3. A button for exporting to Excel.

## Questions

What's the user outcome?

The answers would always be functional answers. "We go in, we press these dropdown buttons and then we export to Excel".

This data would be exported and transformed in other ways into charts etc. to present to senior management.

It's easy to say that we've just not automated the right thing. But these things happen for a few reasons:

1. In big institutions they love their spreadsheets. Some form of spreadsheet app has existed for decades and is there from the first day they join and they have a rich array of analytical capabilities.
    - Jacob's law of UX. They expect the applications they use day-to-day to behave like the applications they've used previously. They want to find the tools they know in very similar places.
    - Data-grids are effectively the same thing and are a comfortable transition away.
2. Time to market pressures.
    - The wrappers they created made it so much easier for engineers to create these grids. A sort of self-fulfilling prophecy.
    - But this can lead to a maintainence nightmare. When you explode the number of grids all of those can be affected.
3. Asking what the desired outcome is very difficult
    - Users saying "I want this" might not be enough.
    - Having a lack of business knowledge is very difficult.
    - Trying to automate an existing process also locks you in a bit
4. People will often say they can't use certain controls because of their own preferences for those controls
    - These opinions get pulled into the designs that you created
    - We automatically think of the previous experiences we've had with specific controls because of past bad experiences

## Why should we care?

Overloading users with data "just in case" they need it can affect their overall focus on the task at hand.

Information overload: The volume of inputs being processed by a particular component exceeds the capacity of that component to process it.

This rule can be applied to people too. Measuring focus is actually quite a difficult question to answer. The research Carly has done has shown this. Irrespective of that, whenever Carly observed their users, they're using more than one app at a time. They're reading lots of documents, other apps etc.

So we need to consider that and make sure that our tool makes it as easy as possible to use.

## Code examples

github.com/carlyrichmond/data-grid-alternatives

## Sorting

It's a common user behaviour to try and find the top or the bottom of something. If you see dips in your data set or things that used to be at the top fall off we want to know why.

If your data is based on numerical structures, you should consider using a bar or a column chart. You can see which of the values are standing out over time.

With charts you need to think about responsiveness. This is not just for desktop vs mobile. Often analysts had smaller monitors than developers.

With bar charts we need to think about stacking. If we're categorising certain items within a chart we can group them, but we should avoid grouping un-related data.

ReCharts - All configuration is in HTML tags
HighCharts - Options based configuration

For longer time series or smaller fluctuations you might want to consider a line chart or area chart. Area charts are better when talking about volume, but line charts are for particular positions. Often you can specify that you x-axis is of the time type in the charting library.

Zoom is good. Highcharts has it built in but Recharts requires you to specify the algorithm for transforming the data for zoom.

## Hiding Data

A common reaction from users is to hide data to only show the subset that is useful.

We need to ask the users if the hiding is for a transformation to another form for consumption, or if it's to make it easier to view.

A summary card is a good way to help with this. We add summary statistics, and maybe even a chart of some sort. You can see your whole portfolio represented in one place for later digging in.

Good examples include MUI Card, Recharts, Material, Elastic.

## Relationships

Relationships between entities. For example the moving of products between accounts. Or sending gift cards to families.

People try this with grids but it doesn't always work out. Collapsible rows works okay but seeing reflexive relationships and relative volumes doesn't quite work.

The core diagram (eg. Nivo) shows arcs on the outside of a ring and then ribbons between the connected individuals. A sankey diagram is another idea.

Advanced chart types like this lead us to think about things like D3S. These often have quite a high learning overhead. When faced with building and maintaining products at work, you often might not want to go in for something like this.

We often add tooltips to our ribbons to give extra information.

## Jurisdictions

Location data often ends up being things like just the City Name or a Flag.

However, we can do things like show maps with bubble charts where the axes are latitude and longitude. This helps us find areas where we have high volumes of distribution. For example in the e-commerce space.

Things you have to think about are:

- Which projection to use
- Geo-information. There are two: topo json (which includes topographical information) and geo-json

We then just have to put on the marker information for each of the bubbles at certain location.

## When can I use a datagrid?

Sometimes you have parts of the organisation that need more flexible report requirements. They need to be able to generate their own type of reports.

However, if you let them take that into spreadsheets you lose the ability to get insights and user statistics.

Look at options for being able to add things like pivot tables. AG Grid Enterprise has that support. You can also create profiles so that they can share them amongst themselves.

Often it doesn't have to be one or the other. You can give users a grid that is driven by selections made in visualisations.

To do this you'll need a call back to the click event on the chart. You'll then need to figure out how to pull out the driving data from the selected item.

Then in the grid, let's call the grid native filtering instead of trying to manipulate the data underneath. Then invoke the onFilterChanged event.

## Further Inspiration

- Data Sketches by Nadieh Bremer and Shirley Wu
- Information is Beautiful by David McCandless
- D3 Graph Gallery
- Newspaper Infographics
- Websites
- Code Samples

## Questions

When is the right time to use a chart vs a grid?

- Consider the audience. If the data immediately comes out of the grid and gets transformed, you've automated the detail but not the high level statistics.

How do you deal with high data volumes?

- Consider a few options
    - Can you bucket the data? You can group the volume and then drill down into those buckets
    - If you're thinking about data across a distribution, you might want to consider a scatter plot although for high volumes this can require performance testing.
