#include<iostream>
#include "m1.h"
#include <math.h>
#include <string>
#include <algorithm>
#include<unordered_map>
#include<unordered_set>

using namespace std;
// Global Variables
unordered_map<string,unsigned> street_ids_from_name;
unordered_map<string,unsigned> intersections_id_from_name;
unordered_map<string,vector<unsigned>> all_segments_in_street_from_name;

// Implementation of the Hash Tables written above.
bool load_map(std::string map_name) 
{
    bool load_success = loadStreetDatabaseBIN(map_name);
    
    // Hash Table for StreetNames, get street id by doing street_ids_from_name[string here]
    for(unsigned i = 0, j = 0; i < getNumberOfStreets(); i++, j++)
    {
        street_ids_from_name.insert(make_pair(getStreetName(i),i));
    }
    // Hash Table for StreetNames, get intersection id by doing intersections_id_from_name[string here]
    for(unsigned i = 0; i < getNumberOfIntersections(); i++)
    {
        intersections_id_from_name.insert(make_pair(getIntersectionName(i),i));
    }

    //all_segments_in_street_from_name[street name here] will return a vector with street segments
    for(unsigned i = 0; i < getNumberOfStreetSegments(); i++)
    {     
        string street_name = getStreetName(getStreetSegmentStreetID(i));
        auto find_street_name = all_segments_in_street_from_name.find(street_name);
        
        if(find_street_name != all_segments_in_street_from_name.end())
        {
            find_street_name-> second.push_back(i);
        }
        else
        {
            vector<unsigned> street_segments;
            street_segments.push_back(i);
            all_segments_in_street_from_name.insert(make_pair(street_name, street_segments));
        }
    }
   // string name1 = getIntersectionName(66102);
  //  string name2 = getIntersectionName(66103);
  //  cout<<name1<<endl<<name2;
  // string name = getIntersectionName(66103);
  //cout<<find_adjacent_intersections(name).size()<<endl;
   /*
   cout<<find_adjacent_intersections(name)[0]<<endl;
   cout<<find_adjacent_intersections(name)[1]<<endl;
   cout<<find_adjacent_intersections(name)[2]<<endl;

   cout<<"Intersection Name: "<<name<<endl;*/
   //for (vector<unsigned>::iterator itr = find_adjacent_intersections(name).begin(); itr != find_adjacent_intersections(name).end(); ++itr)
  // for(unsigned i=0; i<find_adjacent_intersections(name).size(); i++)
  // {
//      cout<<"Intersection Name: "<<name<<" "<<*itr<<endl;
  //     if(find_adjacent_intersections(name)[i] != NULL)
  //     cout<<find_adjacent_intersections(name)[i]<<endl;
  // }
    
     //string intersection_name = getIntersectionName(66102);
     //    cout<<intersection_name<<endl;
    //if(intersection_name != "")
   // {
       //for(unsigned i=0; i<find_adjacent_intersections(intersection_name).size(); i++)
       //{
      //     cout<<"heym1"<<endl;
        //if(find_adjacent_intersections(intersection_name)[i] != NULL)
        //cout<<find_adjacent_intersections(intersection_name)[i]<<endl;
        //}
   // }
     //cout<<endl;
    
    return load_success;
}

//close the map
void close_map() {
    closeStreetDatabase();
    
}

//function to return intersection id for an intersection name
unsigned find_intersection_id_from_name(std::string intersection_name)
{
    /*	
	For a given intersection name, extract intersection id using
	the unordered map called <intersection_id_from_name>
    */
    unsigned get_intersection_id;
    get_intersection_id = intersections_id_from_name[intersection_name];
    return get_intersection_id;
}

//function to return street id for an intersection name
unsigned find_street_id_from_name(std::string street_name)
{
    /*
	For a given street name, extract street id using the
	unordered map called <street_ids_from_name>
    */    
    unsigned get_street_id;
    get_street_id = street_ids_from_name[street_name];
    return get_street_id;
}

//function to return the street segments for a given intersection 
std::vector<unsigned> find_intersection_street_segments(std::string intersection_name)
{
    /*
	For a given intersection name, extract intersection id using the
	unordered map called <intersection_ids_from_name>
    */ 
    unsigned intersection_id = intersections_id_from_name[intersection_name];
    return find_intersection_street_segments(intersection_id);
    
}


std::vector<unsigned> find_intersection_street_segments(unsigned intersection_id)
{
    vector<unsigned> street_segments;
    // Loop through all street segments at an intersection
    for(unsigned intersection_segment_index = 0; intersection_segment_index < getIntersectionStreetSegmentCount(intersection_id); intersection_segment_index++)
    {
        // Get the segment id and insert it in the vector
        unsigned segment_id = getIntersectionStreetSegment(intersection_id, intersection_segment_index);
        street_segments.push_back(segment_id);
    }
    return street_segments; 
}

//function to return street names at an intersection
std::vector<std::string> find_intersection_street_names(std::string intersection_name)
{
    std::vector<std::string> connected_street_names;
    unsigned intersection_id = find_intersection_id_from_name(intersection_name);
    
    //call to function (below) which does same function, but with intersection_id as the argument
    connected_street_names = find_intersection_street_names(intersection_id);
    return connected_street_names;
}


std::vector<std::string> find_intersection_street_names(unsigned intersection_id)
{   
    /*
	Iterate through all the streets that meet at a particular intersection.
	Extract name of each street. Before entering each street name in a 
	string vector, check whether it already exists in the vector. Finally, 
	sort the vector in alphabetical order as required by the unit_test.
    */
    std::vector<std::string> connected_street_names;
    for(unsigned intersection_segment_index = 0; intersection_segment_index < getIntersectionStreetSegmentCount(intersection_id); intersection_segment_index++)
    {
        unsigned segment_id = getIntersectionStreetSegment(intersection_id, intersection_segment_index);
        unsigned street_id = getStreetSegmentStreetID(segment_id);
        string street_name = getStreetName(street_id);
        
        /*find() is a vector member function looks for an entry in the vector and returns it.
         If the entry does not exist, it returns the last element in the vector.
        */
        if(find(connected_street_names.begin(), connected_street_names.end(), street_name) == connected_street_names.end())
        {
            connected_street_names.push_back(street_name);
        }
    }
    //output requires sorting this vector
    sort(connected_street_names.begin(), connected_street_names.end());
    return connected_street_names;    
}

//can you get from intersection1 to intersection2 using a single street segment (hint: check for 1-way streets too)
bool are_directly_connected(std::string intersection_name1, std::string intersection_name2)
{
    unsigned intersection_id1 = find_intersection_id_from_name(intersection_name1);
    unsigned intersection_id2 = find_intersection_id_from_name(intersection_name2);
    bool connected = false;
    // Get no. of street segments at first intersection
    unsigned total_intersection_street_segments = getIntersectionStreetSegmentCount(intersection_id1);
    
    if(intersection_id1 == intersection_id2)
    {
        return true;
    }
    // Loop through all street segments of intersection 1
    for(unsigned segment_index = 0; segment_index < total_intersection_street_segments; segment_index++)
        {
        // Get the segment id and get the 2 intersections its connected to.
            unsigned segment_id = getIntersectionStreetSegment(intersection_id1, segment_index);
            StreetSegmentEnds ends = getStreetSegmentEnds(segment_id);
            // If one way street then check if the other end is intersection 2
            if(getStreetSegmentOneWay(segment_id) == true)
            {
                if(ends.to == intersection_id2)
                {
                    connected = true;
                    break;
                }
            }   
        // If 2 way street then check if both intersection ids belong to same street segment.
            else
            {
                if((ends.from == intersection_id1 && ends.to == intersection_id2) || (ends.from == intersection_id2 && ends.to == intersection_id1))
                {
                    connected = true;
                    break; 
                }               
            }
       } // end of for loop
    
    return connected;
}


std::vector<unsigned> find_adjacent_intersections(std::string intersection_name)
{
    unordered_set<unsigned> nearby_unique_intersections;
 
    bool two_way_streets;
    unsigned intersection_id = find_intersection_id_from_name(intersection_name);
    // Loop through all segments for an intersection
    for(unsigned intersection_segment_index = 0; intersection_segment_index < getIntersectionStreetSegmentCount(intersection_id); intersection_segment_index++)
    {
        two_way_streets = false;
        // Get a segment at the intersection and save both its ends in a structure called segment_ends
        unsigned segment_id = getIntersectionStreetSegment(intersection_id, intersection_segment_index);
        StreetSegmentEnds segment_ends = getStreetSegmentEnds(segment_id);
        if(getStreetSegmentOneWay(segment_id) == true)
        {
            if(segment_ends.from == intersection_id)
            {
                nearby_unique_intersections.insert(segment_ends.to);
               // adj_intersections.push_back(segment_ends.to);
            }
                
        }
        /* if 2 way street then check for both ends if any of them equals intersection id then
        add it to the vector */
        else
        {
            if(segment_ends.from == intersection_id)
            {
                nearby_unique_intersections.insert(segment_ends.to);
                two_way_streets = true;
            }           
            
            else if(segment_ends.to == intersection_id && two_way_streets!=true)
            {
                nearby_unique_intersections.insert(segment_ends.from);
            }
        }
    }
    vector<unsigned> adj_intersections(nearby_unique_intersections.size());
    copy(nearby_unique_intersections.begin(), nearby_unique_intersections.end(), adj_intersections.begin());
    
     return adj_intersections;     
}

//for a given street, return all the street segments
std::vector<unsigned> find_street_street_segments(std::string street_name)
{
    /*
	For a given street name, extract street segments using the
	unordered map called <all_segments_in_street_from_name>
    */
    vector<unsigned> street_segments = all_segments_in_street_from_name[street_name];
    return street_segments;    
}

//for a given street, find all the intersections
std::vector<unsigned> find_all_street_intersections(std::string street_name)
{
    // Use of set because set inserts only unique elements and ignores redundant ones.
    unordered_set<unsigned> set_of_street_intersections;
    vector<unsigned> streetsegs = all_segments_in_street_from_name[street_name]; 
    // Loop through all street segments of a street
    for (vector<unsigned>::iterator itr = streetsegs.begin(); itr != streetsegs.end(); ++itr)
    {
        // Get both end points of street segments
        StreetSegmentEnds segment_ends = getStreetSegmentEnds(*(itr));
        set_of_street_intersections.insert(segment_ends.from);
        set_of_street_intersections.insert(segment_ends.to);
     
    }    
    // Copy the set elements to a vector because return type is a vector
    vector<unsigned> street_intersections(set_of_street_intersections.size());
    copy(set_of_street_intersections.begin(), set_of_street_intersections.end(), street_intersections.begin());
    return street_intersections;    
}

//find distance between two coordinates
double find_distance_between_two_points(LatLon point1, LatLon point2) {
    double point1_x, point1_y, point2_x, point2_y;
    double point1_lat, point1_lon, point2_lat, point2_lon;
    double lat_avg;
    double distance_between_points;
    
    //This entire function is based off the function given in page 9 of 
    // the ECE297 milestone 1 handout.
    
    //converting points from degrees to radians
    point1_lat = point1.lat * DEG_TO_RAD;
    point1_lon = point1.lon * DEG_TO_RAD;
    point2_lat = point2.lat * DEG_TO_RAD;
    point2_lon = point2.lon * DEG_TO_RAD;

    //calculating lat_avg for use later
    lat_avg = point1.lat + point2.lat;
    lat_avg = lat_avg / 2;
    lat_avg = lat_avg * DEG_TO_RAD;
    //finding the x-coords of point1 and point2
    //find point1_x
    point1_x = cos(lat_avg);
    point1_x = point1_x * point1_lon;
    //find point2_x
    point2_x = cos(lat_avg);
    point2_x = point2_x * point2_lon;
    //finding the y-coords of point1 and point2
    point1_y = point1_lat;
    point2_y = point2_lat;
    //calculating the distance between the points
    //y^2 + x^2
    distance_between_points = (point2_y - point1_y)*(point2_y - point1_y) + (point2_x - point1_x)*(point2_x - point1_x);
    distance_between_points = EARTH_RADIUS_IN_METERS * sqrt(distance_between_points);

    return distance_between_points;
}

//find the length of a given street segment
double find_street_segment_length(unsigned street_segment_id)
{    
    /*
	Find the 2 ends of the street_segment.Get LatLon for the beginning of 
	the street segment and insert it into a "LatLon" vector. Iterate through 
	all the curve points of the street segment, get LatLon of each curve point
	and calculate distance between	2 points. Add all the distances. Finally, 
	add the distance between the second-last point and the end point of the segment.
    */

    long double street_seg_length = 0.00;
    vector<LatLon> curve_point_vec;
    LatLon temp_latlon;
    //find which 2 intersections is this street segment connected to
    StreetSegmentEnds segment_ends = getStreetSegmentEnds(street_segment_id);
    
    //Get LatLon for the beginning of the street segment
    curve_point_vec.push_back(getIntersectionPosition(segment_ends.from));

    unsigned total_curve_points = getStreetSegmentCurvePointCount(street_segment_id);

    //Get LatLon for the curve points of the street segment
    for(unsigned i = 0; i < total_curve_points; i++)
    {
        temp_latlon = getStreetSegmentCurvePoint(street_segment_id, i);
        curve_point_vec.push_back(temp_latlon);
        street_seg_length += find_distance_between_two_points(curve_point_vec[i], curve_point_vec[i+1]);
    }
    
    //Get LatLon for the end of the street segment
    curve_point_vec.push_back(getIntersectionPosition(segment_ends.to));  
    street_seg_length += find_distance_between_two_points(curve_point_vec[total_curve_points], curve_point_vec[total_curve_points+1]);
 
    return street_seg_length;
}

//find the length of a whole street
double find_street_length(std::string street_name)
{
    /*
	Extract all the street segment id from a unordered map called <all_segments_in_street_from_name>.
	Iterate through the vector containing all streets segment ids. Find distance of each 
	street segment. Add all the individual distance and return total_street_length.
    */

    double total_street_length = 0.00;
    
    vector<unsigned> street_seg_vec = all_segments_in_street_from_name[street_name]; 
    
    for(vector<unsigned>::iterator iter = street_seg_vec.begin(); iter != street_seg_vec.end(); iter++)    
    {
        total_street_length += find_street_segment_length(*iter);
    }
    
    return total_street_length;
}


double find_segment_travel_time(unsigned street_segment_id)
{
    /*	
	Find speed of the street segment from street_segment_id. Find
	length of the street segment. Use the following formula:    
	
	Formula: time(minutes) = (distance(Km)/speed_limit(Km/h))*60
    */
    
    double speed_limit = getStreetSegmentSpeedLimit(street_segment_id);
    
    /*NOTE: distance from find_street_segment_length is in METERS;
            whereas, this function requires distance in KILOMETERS */
    
    double temp_dist = find_street_segment_length(street_segment_id);
    
    double street_seg_dist = temp_dist/1000;
    double travel_time = (street_seg_dist/speed_limit) * 60;
    return travel_time;
}


std::string find_closest_point_of_interest(LatLon my_position)
{
    unsigned PoI_index, closest_PoI_index=0;    //(P)oint (o)f (I)nterest index
    double PoI_distance = EARTH_RADIUS_IN_METERS; //Initialize with a very large value
    LatLon PoI_location;
    
    //Loop through the whole list of PointsOfInterest, and while doing that, 
    //calculate the distance between the current point of interest and my_position.
    //If it's less than the smallest distance so far, save the the PoI index, as
    //well as the distance. At the end of the loop, return the name of the closest PoI.
    for(PoI_index = 0; PoI_index < getNumberOfPointsOfInterest(); PoI_index++)
    {
        //Gets the PoI location from the index, and find the distance from my_position
        PoI_location = getPointOfInterestPosition(PoI_index);
        double tempPoI_distance = find_distance_between_two_points(my_position, PoI_location);
        
        //'Saves' the current PoI_distance and PoI_index, and updates if a smaller distance is found.
        if(tempPoI_distance < PoI_distance)
        {
            PoI_distance = tempPoI_distance;
            closest_PoI_index = PoI_index;
        }
    }
    return getPointOfInterestName(closest_PoI_index);
}

unsigned getSize(string street_name)
{
    vector<unsigned> street_seg_vec = all_segments_in_street_from_name[street_name];
    return street_seg_vec.size();
}