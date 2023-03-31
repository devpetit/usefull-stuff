# Authorization in dotnet

[StackOverflow](https://stackoverflow.com/questions/22814023/role-based-access-control-rbac-vs-claims-based-access-control-cbac-in-asp-n)

## What Are Roles

Role = The union of Users and Permissions.

On one hand, a Role is a collection of Permissions. I like to call it a Permission Profile. When defining a Role you basically add a bunch of Permissions into that Role so in that sense a Role is a Permission Profile.

On the other hand, a Role is also a collection of Users. If I add Bob and Alice to the Role "Managers" then "Managers" now contains a collection of two Users sort of like a Group.

The truth is that a Role is BOTH a collection of Users and a collection of Permissions put together. Visually this can be viewed as a Venn diagram.

## What is a Group

Group = Collection of Users

A "Group" is strictly a collection of Users. The difference between a Group and a Role is that a Role also has a collection of Permissions but a Group only has a collection of Users.

## What is a Permission

Permission = What a subject can do

## What is a Permission Set

Permission Set = A Collection of Permissions

In a robust RBAC system, Permissions can also be grouped like Users. Whereas Groups are a collection of Users only, a Permission Set is a collection of Permissions only. This allows an administrator to add whole collections of Permissions to Roles at one time.

## How Users, Groups, Roles, and Permissions Come Together

In a robust RBAC system, Users can be added to a Role individually to create the collection of Users in the Role or Groups can be added to a Role to add a collection of Users to the Role at one time. Either way, the Role gets its collection of Users from being individually added or by adding Groups to the Role or by adding a mix of Users and Groups to the Role. Permissions can be thought of in the same way.

Permissions can be added to Roles individually to create the collection of Permissions inside the Role or Permission Sets can be added to a Role. Finally, a mix of Permissions and Permission Sets can be added to a Role. Either way, the Role gets its collection of Permissions from being individually added or by adding Permission Sets to a Role.

The whole purpose of Roles is to marry Users to Permissions. Therefore, a Role is the UNION of Users and Permissions.

## What Are Claims

Claim = What a Subject "is" (like: "Senior-Employee", "Award-Winner-Employee", "Experienced-On-Sales")

Claims are NOT Permissions. As pointed out in previous answers, a Claim is what a subject "is" not what a subject "can do".

Claims do not replace Roles or Permissions, they are additional pieces of information that one can use to make an Authorization decision.

When to Use Claims

I have found Claims to be useful when an Authorization decision needs to be made when the User cannot be added to a Role or the decision is not based on the association of User to Permission. The example of a Facebook User causes this. A Facebook User may not be someone who is added to a "Role" ... they are just some Visitor authenticated through Facebook. Though it doesn't fit neatly into RBAC it is a piece of information to make a authorization decision on.

@CodingSoft used the night club metaphor in a previous answer, which I'd like to extend. In that answer, the Driver's License was used as an example that contained a set of Claims where the Date of Birth represents one of the Claims and the value of the DateOfBirth Claim is used to test against the authorization rule. The government that issued the Driver's License is the authority that gives the Claim authenticity. Therefore, in a night club scenario, the bouncer at the door looks at the the person's Driver's License, ensures that it was issued by a trusted authority by examining whether or not it is a fake ID (i.e. must be valid government issued ID), then looks at the Date of Birth (one of the many claims on a Driver's License), then uses that value to determine if the person is old enough to enter the club. If so, the person passes the authorization rule by virtue of having a valid Claim, not by being in some Role.

Now, with that base in mind I'd like to now extend that further. Suppose that the building where the night club is contains offices, rooms, a kitchen, other floors, elevators, a basement, etc. where only employees of the club can enter. Furthermore, certain employees might have access to certain places that other employees may not. For example, a Manager may have access to an office floor above that other employees cannot access. In this case there are two Roles. Manager and Employee.

While visitors' access to the public night club area is authorized by a single claim as explained above, employees need access by Role to other non-public restricted rooms. For them, a Driver's License is not enough. What they need is an Employee Badge that they scan to enter doors. Somewhere there is an RBAC system that grants badges in the Manager Role access to the top floor, and badges in the Employee Role access to other rooms.

If for whatever reason certain rooms need to be added/removed by Role, this can be done using RBAC, but it is not a good fit for a Claim.