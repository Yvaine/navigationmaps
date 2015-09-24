#pragma once                                                                                                                                                                                                                                                                                                e
#include "StreetsDatabaseAPI.h"
#include <vector>
#include <string>
#include <m1.h>
#include <m2.h>
#include <m3.h>
#include <queue> 
#include <unordered_map>
#include <algorithm>
#include <list>
using namespace std;
// Returns a path (route) between the start intersection and the end 
// intersection, if one exists. If no path exists, this routine returns 
// an empty (size == 0) vector. If more than one path exists, the path 
// with the shortest travel time is returned. The path is returned as a vector 
// of street segment ids; traversing these street segments, in the given order,
// would take one from the start to the end intersection.

vector<unsigned> find_path_between_intersections(unsigned intersect_id_start, unsigned intersect_id_end) 
{
    queue<unsigned> nodes_queue; //contains all nodes that we will visit
    list<unsigned> is_node_visited; //nodes that have been visited
    unordered_map<unsigned,unsigned> prev_node_visited, temp_street_segments;
    bool found = false;
    vector<unsigned> final_path, final_path_street_segments;
    
    if(intersect_id_start == intersect_id_end) 
    {
        final_path.push_back(intersect_id_start);
        return final_path;
    }
    
    unsigned source, destination;
    source = intersect_id_start;
    nodes_queue.push(source);  // will visit this node 
    is_node_visited.push_back(source); //marked as visited

    while(!nodes_queue.empty())
    {
      // remove the node at the top of the queue and find its adjacent intersections
      source = nodes_queue.front();
      nodes_queue.pop();
      unsigned next_node_id;
      string intersection_name = getIntersectionName(source);
      
      // get the vector with all the street segments, for use in the next for loop.
      vector<unsigned> intersection_street_segments = find_intersection_street_segments(intersection_name);
      //cout<<"Sources"<<endl;
        for(unsigned i = 0; (i < intersection_street_segments.size()); i++)
        {
         // check if adjacent intersection has not already been visited
            StreetSegmentEnds street_seg_ends = getStreetSegmentEnds(intersection_street_segments[i]);
            
            //checks to see which intersection we're currently looking at, and check the next node id.
            if(getStreetSegmentOneWay(intersection_street_segments[i]) == true)
            {
                next_node_id = street_seg_ends.to;
            }
            else
            {
                if(street_seg_ends.from == source)
                {
                    next_node_id = street_seg_ends.to;
                }
                else if (street_seg_ends.to == source)
                {
                    next_node_id = street_seg_ends.from;
                }
            }
            
            // check to see if the next_node_id is already on the list or not.
            auto find_node = find(is_node_visited.begin(), is_node_visited.end(), next_node_id);
          
            //if not visited then put it in queue and mark it as visited.
            if(find_node == is_node_visited.end())
            {
                nodes_queue.push(next_node_id);
                is_node_visited.push_back(next_node_id);
                //mark how I reached this node, to trace the path later
                prev_node_visited.insert(make_pair(next_node_id, source));
                temp_street_segments.insert(make_pair(next_node_id, intersection_street_segments[i]));
                //cout<<"source: "<<source<<endl;
            }
            // if I found the end node then stop
            if(next_node_id == intersect_id_end)
            {
                destination = next_node_id;
                found = true;
            }
        } // end of for loop
      
      // Checks only first iteration.
      if(found)
      {
          break;
      }
    } // end of while loop
    
    if(found == true)
    {
        final_path.push_back(destination);
        //trace the path
        while(destination != intersect_id_start)
        {
            final_path.push_back(prev_node_visited[destination]);
            final_path_street_segments.push_back(temp_street_segments[destination]);
            destination = prev_node_visited[destination];
        }
    }
    else
    {
        // if no path exists between source and destination
        final_path.clear();
        final_path_street_segments.clear();
    }
    
    // reverse the path so that nodes come from start to end
   reverse(final_path_street_segments.begin(),final_path_street_segments.end());
   
   return final_path_street_segments;
}

/*unsigned find_segment_id(unsigned intersection_id_1, unsigned intersection_id_2) {
    string intersection_name = getIntersectionName(intersection_id_1);
    vector<unsigned> street_segments = find_intersection_street_segments(intersection_id_1);
    for (unsigned i = 0; (i < street_segments.size()); i++) {
        if (getStreetSegmentOneWay(street_segments[i]) == true) {

            if (getStreetSegmentEnds(street_segments[i]).to == intersection_id_2) {
                return street_segments[i];
            }
        } else {
            if (getStreetSegmentEnds(street_segments[i]).to == intersection_id_2 || getStreetSegmentEnds(street_segments[i]).from == intersection_id_2) {
                return street_segments[i];
            }
        }
    }

}*/

// Returns the time required to travel along the path specified. The path
// is passed in as a vector of street segment ids, and this function can 
// assume the vector either forms a legal path or has size == 0.
// The travel time is the sum of the length/speed-limit of each street 
// segment, plus 15 seconds per turn implied by the path. A turn occurs
// when two consecutive street segments have different street names.

double compute_path_travel_time(const std::vector<unsigned>& path)
{
    
    double time_taken = 0.0;
    unsigned temp_street_id;
    
    //if the path is an illegal one, time = 0 and return immediately
    if(path.size() == 0)
    {
        return time_taken;
    }
    //find_segment_travel_time returns time in minutes 
    //Formula for find_travel_time function: time(minutes) = (distance(Km)/speed_limit(Km/h))*60
    time_taken = find_segment_travel_time(path[0]) * 60;//in seconds
    temp_street_id = path[0];
    
    for(unsigned i = 1; i < path.size(); i++)
    {
        if(getStreetName(getStreetSegmentStreetID(temp_street_id)) == getStreetName(getStreetSegmentStreetID(path[i])))//if path[i] == path[i-1], no 15s turn penalty
        {          
            time_taken += find_segment_travel_time(path[i]) * 60;//in seconds
        }
        
        //A turn occurs when two consecutive street segments have different street names
        else
        {
            time_taken += find_segment_travel_time(path[i]) * 60;//in seconds
            time_taken += 15;//penalty for making a turn; this is in seconds(15s)
        }
        
        temp_street_id = path[i];//save the previous street_id to compare it to the next one
    }
    
    return time_taken;
}


// Returns the shortest travel time path (vector of street segments) from 
// the start intersection to a point of interest with the specified name.
// If no such path exists, returns an empty (size == 0) vector.

//std::vector<unsigned> find_path_to_point_of_interest (unsigned 
  //                 intersect_id_start, std::string point_of_interest_name);


// Function that draws the path between two points, specified by the vector "path"
// which includes all the intersections on a path between the source and destination.
void draw_path (vector<unsigned> path)
{
    t_point intersection_coords_xy1, intersection_coords_xy2;
    
    // If the the two intersections specified are the same point.
    if(path.size() == 0 || path.size() == 1)
    {
        return;
    }
    // Else if there's only two points in the path: the source and destination
    else if(path.size() == 2)
    {
        // Get the intersection XY coords of both the source and destination, and
        // draw them directly with drawline.
        intersection_coords_xy1 = latlon_to_xy(getIntersectionPosition(path[0]));
        intersection_coords_xy2 = latlon_to_xy(getIntersectionPosition(path[1]));
        
        drawline(intersection_coords_xy1.x, intersection_coords_xy1.y, intersection_coords_xy2.x, intersection_coords_xy2.y);
    }    
}